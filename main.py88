import re
import logging
import telebot
from flask import Flask, request, abort

TOKEN = "8303813448:AAEVDY4a5fzP7pT-Yq-yPfdkzU0EsO87Z1c"
WEBHOOK_URL = "https://soodajiye-bot.onrender.com"

bot = telebot.TeleBot(TOKEN)
app = Flask(__name__)

@bot.message_handler(
    func=lambda m: m.chat.type in ["group", "supergroup"] and m.content_type == 'text'
)
def anti_spam_filter(message):
    try:
        bot_member = bot.get_chat_member(message.chat.id, bot.get_me().id)
        if bot_member.status not in ['administrator', 'creator']:
            return
        user_member = bot.get_chat_member(message.chat.id, message.from_user.id)
        if user_member.status in ['administrator', 'creator']:
            return
        text = message.text or ""
        if (
            len(text) > 120
            or re.search(r"https?://", text)
            or "t.me/" in text
            or re.search(r"@\w+", text)
        ):
            bot.delete_message(chat_id=message.chat.id, message_id=message.message_id)
    except Exception as e:
        logging.warning(f"Anti-spam check failed: {e}")

def set_bot_info():
    cmds = [
        telebot.types.BotCommand("start", "Start the bot"),
        telebot.types.BotCommand("help", "Show help message")
    ]
    bot.set_my_commands(cmds)
    bot.set_my_description("To keep your Telegram groups clean, focused, and free from spam or excessively long messages that disrupt conversations, use this bot.")

@bot.message_handler(commands=['start'])
def handle_start(message):
    bot.send_message(
        message.chat.id,
        "isticmaal bot kaas 👉🏻https://t.me/Video_DownloadeBot asaga u dir link ga asagaa kuu soodaji naayo video gee"
    )

@bot.message_handler(commands=['help'])
def handle_help(message):
    bot.send_message(
        message.chat.id,
        "Commands:\n"
        "/start - Start bot\n"
        "/help - This help message\n\n"
        "This bot only removes spam from groups when it is an admin.",
        parse_mode="Markdown"
    )

@bot.message_handler(func=lambda m: m.chat.type == 'private' and not (m.content_type == 'text' and m.text and m.text.strip().split()[0].lower() == '/start'))
def welcome_message(message):
    bot.send_message(
        message.chat.id,
        "isticmaal bot kaas 👉🏻https://t.me/Video_DownloadeBot asaga u dir link ga asagaa kuu soodaji naayo video gee"
    )

@app.route('/', methods=['POST'])
def webhook():
    if request.headers.get('content-type') == 'application/json':
        update = telebot.types.Update.de_json(request.data.decode('utf-8'))
        bot.process_new_updates([update])
        return '', 200
    else:
        abort(403)

@app.route('/set_webhook', methods=['GET'])
def set_wh():
    bot.set_webhook(url=WEBHOOK_URL)
    return f"Webhook set to {WEBHOOK_URL}"

@app.route('/delete_webhook', methods=['GET'])
def del_wh():
    bot.delete_webhook()
    return "Webhook deleted"

if __name__ == "__main__":
    set_bot_info()
    bot.set_webhook(url=WEBHOOK_URL)
    app.run(host="0.0.0.0", port=int(__import__('os').environ.get("PORT", 8080)))
