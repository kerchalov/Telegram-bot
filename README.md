# Telegram-bot
import telebot
import sqlite3
from datetime import datetime

bot = telebot.TeleBot("8291551106:AAFwBn4YXA-uRx9eUAA1KKzG7fSesObOHBw")

conn = sqlite3.connect('transaction_info_list.sql')
cur = conn.cursor()

cur.execute('CREATE TABLE IF NOT EXISTS transaction_info (id int auto_increment primary key, money int, info varchar(50), datetime varchar(50))')
cur.execute('INSERT INTO transaction_info (money) VALUES (0)')
conn.commit()
cur.close()
conn.close()

money = 0
info = ''
last_transaction = None
transaction_list = None

@bot.message_handler(commmands=['start'])
def start(message):
    bot.sendmessage(message.chat.id, 'Кошелек готов к использованию')

@bot.message_handler(commmands=['add_transaction'])
def add_transaction(message):
    bot.sendmessage(message.chat.id, 'Отправьте тразнакцию')
    bot.register_next_step_handler(message, add_money)

def add_money(message):
    global money
    global conn
    global cur
    global last_transaction
    cur = ('SELECT * FROM transaction_info ORDER BY ROWID DESC LIMIT 1')
    last_transaction = cur.fetchone()
    money = int(message.text) + last_transaction[1]
    bot.sendmessage(message.chat.id, 'Отправьте информацию о транзакции')
    bot.register_next_step_handler(message, add_transaction_info)

def add_transaction_info(message):
    global info
    global conn
    global cur
    info = message.text.strip().lower()
    now = datetime.now()

    conn = sqlite3.connect('transaction_info_list.sql')
    cur = conn.cursor()

    cur.execute('INSERT INTO transaction_info (money, info, datetime) VALUES ("%s", "%s", "%s")' % (money, info, now))
    conn.commit()
    cur.close()
    conn.close()

    bot.sendmessage(message.chat.id, 'Все готово')

@bot.message_handler(commmands=['show_last_transaction'])
def show_balance(message):
    global conn
    global cur

    conn = sqlite3.connect('transaction_info_list.sql')
    cur = conn.cursor()

    cur.execute('')
    conn.commit()
    cur.close()
    conn.close()

    bot.sendmessage(message.chat.id, 'ващ баланс на кошельке:')

@bot.message_handler(commmands=['find_trnc_by_date'])
def find_trnc_by_date(message):
    global conn
    global cur
    global transaction_list

    conn = sqlite3.connect('transaction_info_list.sql')
    cur = conn.cursor()
    cur = ('SELECT * FROM transaction_info ORDER BY ROWID DESC LIMIT 1')

    transaction_list = cur.fetchall()

    bot.sendmessage(message.chat.id, )

    cur.execute('')
    conn.commit()
    cur.close()
    conn.close()


# @bot.message_handler(commmands=['find_trnc_by_time'])
# def find_trnc_by_time(message):
#     pass
#
# @bot.message_handler(commmands=['find_trnc_by_info'])
# def find_trnc_by_info(message):
#     pass
