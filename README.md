# Install the plaid-python package
# pip install plaid-python

from plaid import Client
from plaid.errors import PlaidError

# Replace with your Plaid client ID and secret
PLAID_CLIENT_ID = 'your_client_id'
PLAID_SECRET = 'your_secret'
PLAID_ENV = 'sandbox'  # Use 'development' or 'production' for real environments

client = Client(client_id=PLAID_CLIENT_ID, secret=PLAID_SECRET, environment=PLAID_ENV)

# Step 1: Create a Link token to authenticate users
def create_link_token():
    try:
        response = client.LinkToken.create({
            'user': {
                'client_user_id': 'unique_user_id',
            },
            'client_name': 'Your App Name',
            'products': ['auth', 'transactions'],
            'country_codes': ['US'],
            'language': 'en',
        })
        return response['link_token']
    except PlaidError as e:
        print(f"Error creating link token: {e}")
        return None

# Step 2: Exchange the public token for an access token
def exchange_public_token(public_token):
    try:
        response = client.Item.public_token.exchange(public_token)
        return response['access_token']
    except PlaidError as e:
        print(f"Error exchanging public token: {e}")
        return None

# Step 3: Retrieve transactions
def get_transactions(access_token):
    try:
        response = client.Transactions.get(access_token, start_date='2023-01-01', end_date='2023-12-31')
        return response['transactions']
    except PlaidError as e:
        print(f"Error retrieving transactions: {e}")
        return None

# Example Usage
link_token = create_link_token()
print(f"Link token: {link_token}")

# After user authenticates with the link token, you'd receive a public token (mock example):
public_token = 'mock_public_token'

access_token = exchange_public_token(public_token)
if access_token:
    transactions = get_transactions(access_token)
    print(f"Transactions: {transactions}")
