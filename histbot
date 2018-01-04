#!/usr/bin/env python3
import discord
from discord.ext import commands
import asyncio
import sqlite3
import datetime
import contextlib
import argparse
import os, sys

parser = argparse.ArgumentParser(
        prog='histbot',
        description='A bot to scrape the full history from a discord channel')
parser.add_arguments('--token','-t',
        nargs=1,
        type=str,
        required=True
        )
parser.add_arguments('--output','-o',
        nargs=1,
        type=str,
        required=True
        )
parser.add_arguments('--userid','-u',
        nargs=2,
        type=int,
        required=True
        )
args = parser.parse_args(sys.argv)


bot = commands.Bot(command_prefix='!')

@bot.event
async def on_ready():
    print('Logged in as')
    print(bot.user.name)
    print(bot.user.id)
    print('------')


@bot.command(pass_context=True)
async def history(ctx):
    # Permission check
    if ( ctx.message.author.id != args['--userid']): return
    start_time = datetime.datetime.now()
    with contextlib.closing(sqlite3.connect(args['--output')) as dbcon:
        with dbcon.cursor() as cursor:
            while True:
                print('collecting')
                n = 0
                async for message in bot.logs_from(ctx.message.channel,before=start_time):
                    add_message(cursor,message)
                    last_message = message
                    n += 1
                    print(message.timestamp.isoformat(), message.content)
                if n == 0: break
                start_time = last_message.timestamp
                print('sleep')
                await asyncio.sleep(5)
    print('done')


def add_user(cursor, user):
    print('adding user {}'.format(user.name))
    cursor.execute(('INSERT INTO users '
        '( guild_id, username, discriminator) '
        'VALUES (?,?,?)'),
        (int(user.id),str(user.name),int(user.discriminator)))

def add_message(cursor, message):
    c = cursor
    c.execute(('SELECT EXISTS( '
        'SELECT user_id '
        'FROM users '
        'WHERE user_id=?) '),
        (int(message.author.id),)
        )
    if not c.fetchone():
        add_user(c,message.author)
    c.execute(('INSERT OR IGNORE INTO messages '
        '( message_id, user_id, channel_id, datetime, content) '
        'VALUES ( ?, ?, ?, ?, ? )'),
        (int(message.id), int(message.author.id), int(message.channel.id),
            message.timestamp.isoformat(), str(message.content)))

def init_db():
    db_con = sqlite3.connect('database.sqlite')
    c = db_con.cursor()
    c.execute('''CREATE TABLE messages (
            message_id INTEGER PRIMARY KEY,
            user_id INTEGER NOT NULL,
            channel_id INTEGER NOT NULL,
            datetime TEXT NOT NULL,
            content TEXT NOT NULL)''')

    c.execute('''CREATE TABLE users (
            user_id INTEGER PRIMARY KEY,
            username TEXT NOT NULL,
            discriminator INTEGER NOT NULL )''')

if not os.path.isfile(args['--output']):
    init_db()

bot.run(args['--token'])
dbcon.close()
