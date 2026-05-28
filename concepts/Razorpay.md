# ===========================================
# SIMPLE RAZORPAY INTEGRATION GUIDE (REACT + DJANGO REST FRAMEWORK)
# ===========================================
# This single YAML file explains exactly how to connect Razorpay safely 
# using a React frontend and a Django backend.
# ===========================================

metadata:
  title: "Easy Razorpay Guide"
  frontend: "React.js"
  backend: "Django REST Framework (DRF)"
  security_type: "HMAC-SHA256 Signature Verification"

the_simplest_explanation:
  analogy: |
    Think of buying a movie ticket. You can't just write "Paid" on a piece of paper 
    and hand it to the usher—the theater needs to print a barcode that only their 
    scanners can verify. 
    
    Similarly, we never let the frontend (React) decide if a payment was successful. 
    React gets a "receipt stub" (signature) from Razorpay, and gives it to the 
    backend (Django). Django recalculates the security math to ensure nobody cheated.

how_the_money_flows:
  step_1: "React tells Django: 'I want to buy something for ₹500'."
  step_2: "Django talks to Razorpay's servers and says: 'Create a unique Order ID for ₹500'."
  step_3: "Django sends that Order ID back to React. React opens the Razorpay pop-up window."
  step_4: "The customer pays inside the pop-up. Razorpay gives React 3 codes: Order ID, Payment ID, and a Signature."
  step_5: "React sends those 3 codes to Django. Django runs a security check (HMAC). If it passes, Django saves it in the database and says: 'Payment Successful!'."

the_security_math_made_easy:
  what_is_hmac: "HMAC-SHA256 is just a one-way security blender."
  how_django_checks_it: |
    Razorpay gives Django a secret key that only they share. 
    When a payment is done, Django takes the (Order_ID + Payment_ID) and mixes it with the Secret Key inside the blender.
    If Django's blended result matches the 'Signature' sent by React, the payment is 100% genuine.

backend_django_setup:
  required_packages:
    - "razorpay"
    - "djangorestframework"
  
  secret_settings:
    RAZORPAY_KEY_ID: "your_public_key_here"
    RAZORPAY_KEY_SECRET: "your_private_secret_here"

  code_views_py: |
    import razorpay
    from django.conf import settings
    from rest_framework.views import APIView
    from rest_framework.response import Response
    from rest_framework import status

    # Connect to Razorpay using your secret keys
    client = razorpay.Client(auth=(settings.RAZORPAY_KEY_ID, settings.RAZORPAY_KEY_SECRET))

    class CreateOrderView(APIView):
        """
        STEP 1: Tell Razorpay we are about to request a payment
        """
        def post(self, request):
            amount_in_rupees = request.data.get('amount')
            
            # Razorpay works in Paisa (1 Rupee = 100 Paisa)
            amount_in_paisa = int(float(amount_in_rupees) * 100)
            
            order_options = {
                "amount": amount_in_paisa,
                "currency": "INR",
                "receipt": "receipt_id_123",
                "payment_capture": 1 # 1 means automatically take the money immediately
            }
            
            try:
                razorpay_order = client.order.create(data=order_options)
                return Response(razorpay_order, status=status.HTTP_201_CREATED)
            except Exception as e:
                return Response({"error": str(e)}, status=status.HTTP_400_BAD_REQUEST)

    class VerifyPaymentView(APIView):
        """
        STEP 5: Check the Razorpay math to ensure no fraud happened
        """
        def post(self, request):
            # Get the tokens sent from the React frontend
            param_dict = {
                'razorpay_order_id': request.data.get('razorpay_order_id'),
                'razorpay_payment_id': request.data.get('razorpay_payment_id'),
                'razorpay_signature': request.data.get('razorpay_signature')
            }
            
            try:
                # The SDK automatically mixes the keys together to check if they match perfectly
                client.utility.verify_payment_signature(param_dict)
                
                # SUCCESS! 
                # (This is where you update your database: e.g., Order.is_paid = True)
                return Response({"message": "Payment verified perfectly!"}, status=status.HTTP_200_OK)
                
            except razorpay.errors.SignatureVerificationError:
                # FRAUD DETECTED OR INVALID PAYMENT
                return Response({"error": "Signature mismatch. Fake transaction attempt blocked."}, status=status.HTTP_400_BAD_REQUEST)

frontend_react_setup:
  html_requirement: "Add <script src='https://checkout.razorpay.com/v1/checkout.js'></script> to your public/index.html head tag."
  
  code_payment_component_jsx: |
    import React, { useState } from 'react';

    const SimpleCheckout = () => {
      const [loading, setLoading] = useState(false);

      const openRazorpayPopup = (orderData) => {
        const options = {
          key: "YOUR_RAZORPAY_KEY_ID", // Your public key
          amount: orderData.amount,
          currency: orderData.currency,
          name: "My Online Store",
          description: "Buying Coins/Premium",
          order_id: orderData.id, // This links the popup directly to the Django order
          
          // This function runs automatically when the user clicks 'Pay' successfully
          handler: async function (response) {
            setLoading(true);
            
            // Send the 3 proof codes back to Django for verification
            const verifyResult = await fetch("http://127.0.0.1:8000/api/verify/", {
              method: "POST",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify({
                razorpay_order_id: response.razorpay_order_id,
                razorpay_payment_id: response.razorpay_payment_id,
                razorpay_signature: response.razorpay_signature,
              }),
            });

            if (verifyResult.ok) {
              alert("Woohoo! Payment successfully processed and verified.");
            } else {
              alert("Oops! Backend rejected the payment signature.");
            }
            setLoading(false);
          },
          prefill: {
            name: "Customer Name",
            email: "customer@example.com",
            contact: "9999999999"
          },
          theme: { color: "#3399cc" }
        };

        const rzp = new window.Razorpay(options);
        rzp.open();
      };

      const startPayment = async () => {
        setLoading(true);
        try {
          // Tell Django to talk to Razorpay to start an order for ₹500
          const res = await fetch("http://127.0.0.1:8000/api/create-order/", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ amount: 500 }) 
          });
          const orderData = await res.json();
          
          // Pass the order details to open the window
          openRazorpayPopup(orderData);
        } catch (err) {
          alert("Could not start payment.");
        } finally {
          setLoading(false);
        }
      };

      return (
        <button onClick={startPayment} disabled={loading}>
          {loading ? "Please wait..." : "Pay ₹500"}
        </button>
      );
    };

    export default SimpleCheckout;

golden_rules_for_production:
  - "Never share or upload your RAZORPAY_KEY_SECRET to Github or your React frontend."
  - "Always use environment variables (.env files) to hide your keys."
  - "Test everything using Razorpay's 'Test Mode' before toggling the switch to 'Live Mode'."
