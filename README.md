# Telnyx-POC

from flask import Flask, request, jsonify
import requests
import sys 
print(sys.path)


app = Flask(__name__)

# Set your Telnyx API credentials and phone numbers
TELNYX_API_KEY = 'KEY' # Your Telnyx key
TELNYX_PHONE_NUMBER = '+number' # Your Telnyx phone number
TELNYX_APP_PORT = '5000'
# Function to send an MMS message
def send_mms(to, body):
    url = 'https://api.telnyx.com/v2/messages'
    
    headers = {
        'Content-Type': 'application/json',
        'Authorization': f'Bearer {TELNYX_API_KEY}'
    }
    
    data = {
        "from": TELNYX_PHONE_NUMBER,
        "to": to,
        "body": body
    }
    
    response = requests.post(url, headers=headers, json=data)
    
    if response.status_code == 200:
        print("MMS sent successfully!")
    else:
        print(f"Error sending MMS. Status code: {response.status_code}")

# Webhook endpoint to handle inbound messages
@app.route('/webhook', methods=['POST'])
def handle_inbound_message():
    data = request.json
    
    # Extract relevant information from the inbound message
    from_number = data['data']['payload']['from']
    body = data['data']['payload']['body']
    
    # Process the inbound message
    if "pizza" in body.lower():
        response = "Chicago pizza is the best"
    elif "ice cream" in body.lower():
        response = "I prefer gelato"
    else:
        response = "please send either the word 'pizza' or 'ice cream' for a different response"
    
    # Send response as an MMS
    send_mms(from_number, response)
    
    return jsonify(success=True)

# Run the Flask application
if __name__ == "__main__":
    app.run(port=TELNYX_APP_PORT)
