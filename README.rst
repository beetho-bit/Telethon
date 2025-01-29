from telethon import TelegramClient, events
from telethon.tl.functions.messages import GetHistoryRequest
import logging

# Set up logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Replace these with your own values
API_ID = '22877560'  # From my.telegram.org
API_HASH = '88689ee3649bfb7d5527eb9b4c2b5961'  # From my.telegram.org
PHONE_NUMBER = '+2348102523310'  # Your Telegram account phone number

# Initialize the Telegram client
client = TelegramClient('session_name', API_ID, API_HASH)

# Function to handle new messages
@client.on(events.NewMessage)
async def handle_new_message(event):
    """
    Handle new messages in chats or groups.
    """
    sender = await event.get_sender()
    message = event.message.message

    logger.info(f"New message from {sender.username}: {message}")

    # Example: Respond to a specific command
    if message.lower() == '/start':
        await event.reply('Hello! I am your trading bot. Use /buy to buy new coins.')

    # Example: Buy command
    if message.lower() == '/buy':
        await event.reply('Buying new coins...')
        # Add your trading logic here

# Function to fetch chat history
async def fetch_chat_history(chat_username):
    """
    Fetch the chat history of a specific group or channel.
    """
    chat = await client.get_entity(chat_username)
    messages = await client(GetHistoryRequest(
        peer=chat,
        limit=10,  # Fetch last 10 messages
        offset_date=None,
        offset_id=0,
        max_id=0,
        min_id=0,
        add_offset=0,
        hash=0
    ))
    for message in messages.messages:
        logger.info(f"Message: {message.message}")

# Main function
async def main():
    # Connect to your Telegram account
    await client.start(PHONE_NUMBER)

    # Fetch chat history (example)
    await fetch_chat_history('pump_fun_group')  # Replace with the username of the group or channel

    # Keep the client running
    await client.run_until_disconnected()

# Run the bot
if __name__ == '__main__':
    with client:
        client.loop.run_until_complete(main())
