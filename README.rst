Hist Bot
========

This bot downloads the full history of a discord channel and dumps it to a sqlite database.
This bot has been tested to work with python 3.5.


Usage
-----

Start off by downloading the necessary dependencies.::
    pip install discord

We can start the bot by running.::
    python3 histbot.py -t <bot token> -u <your userid> \\
                       -o <output filename>\\
                       -s <start date in YYYY-MM-DD>

Note that the ``-s`` flag is optional, without this the default is set to current time.

After inviting the bot onto the chat, type::
    !history

If this worked successfully you should start seeing a sprawl of text updating every 5 seconds.
