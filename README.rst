Telethon
========
.. epigraph::

  ⭐️ Thanks **everyone** who has starred the project, it means a lot!

|logo| **Telethon** is an asyncio_ **Python 3**
MTProto_ library to interact with Telegram_'s API
as a user or through a bot account (bot API alternative).

.. important::

    If you have code using Telethon before its 1.0 version, you must
    read `Compatibility and Convenience`_ to learn how to migrate.
    As with any third-party library for Telegram, be careful not to
    break `Telegram's ToS`_ or `Telegram can ban the account`_.

What is this?
-------------

Telegram is a popular messaging application. This library is meant
to make it easy for you to write Python programs that can interact
with Telegram. Think of it as a wrapper that has already done the
heavy job for you, so you can focus on developing an application.


Installing
----------

.. code-block:: sh

  pip3 install telethon


Creating a client
-----------------

.. code-block:: python

    from telethon import TelegramClient, events, sync

    # These example values won't work. You must get your own api_id and
    # api_hash from https://my.telegram.org, under API Development.
    api_id = 12345
    api_hash = '0123456789abcdef0123456789abcdef'

    client = TelegramClient('session_name', api_id, api_hash)
    client.start()


Doing stuff
-----------

.. code-block:: python

    print(client.get_me().stringify())

    client.send_message('username', 'Hello! Talking to you from Telethon')
    client.send_file('username', '/home/myself/Pictures/holidays.jpg')

    client.download_profile_photo('me')
    messages = client.get_messages('username')
    messages[0].download_media()

    @client.on(events.NewMessage(pattern='(?i)hi|hello'))
    async def handler(event):
        await event.respond('Hey!')


Next steps
----------

Do you like how Telethon looks? Check out `Read The Docs`_ for a more
in-depth explanation, with examples, troubleshooting issues, and more
useful information.

.. _asyncio: https://docs.python.org/3/library/asyncio.html
.. _MTProto: https://core.telegram.org/mtproto
.. _Telegram: https://telegram.org
.. _Compatibility and Convenience: https://docs.telethon.dev/en/stable/misc/compatibility-and-convenience.html
.. _Telegram's ToS: https://core.telegram.org/api/terms
.. _Telegram can ban the account: https://docs.telethon.dev/en/stable/quick-references/faq.html#my-account-was-deleted-limited-when-using-the-library
.. _Read The Docs: https://docs.telethon.dev

.. |logo| image:: logo.svg
    :width: 24pt
    :height: 24pt
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
