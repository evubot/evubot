import os
import logging
import requests
from telegram import Update, Voice
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes, MessageHandler, filters

# Tokens
BOT_TOKEN = "8178288529:AAEdaWEXMciqc21pQA4qz8hBvNTjWipmiqk"
OPENROUTER_API_KEY = "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"

# Logger
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

# Start command
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    name = user.first_name or "বন্ধু"
    await update.message.reply_text(f"হ্যালো {name}! আমি EvuBot 🤖\nরিয়া আর ইভুর কথা বলো, আর আমি সব জানি! 💙")

# Main chat handler
async def chat(update: Update, context: ContextTypes.DEFAULT_TYPE):
    msg = update.message.text
    user = update.effective_user
    name = user.first_name or "বন্ধু"

    # রিয়া ইভুর প্রতি ভালোবাসা
    if "রিয়া" in msg or "ইভু" in msg:
        await update.message.reply_text("রিয়া আর ইভু চিরকাল একসাথে থাকবে ইনশাআল্লাহ ❤️")

    # হোয়াটসঅ্যাপ / টেলিগ্রাম লিংক
    elif "whatsapp" in msg.lower():
        await update.message.reply_text("এই নাও আমার WhatsApp নম্বর: 01755901536 📱")
    elif "telegram" in msg.lower():
        await update.message.reply_text("আমার Telegram লিংক: https://t.me/tmrevu 📲")
    elif "tiktok" in msg.lower():
        await update.message.reply_text("আমার TikTok ID: tmr___evu 🎵")
    elif "facebook" in msg.lower():
        await update.message.reply_text("Facebook: https://facebook.com/evubot 🌐")

    # সবকিছু AI দিয়ে উত্তর
    else:
        await update.message.reply_text("⏳ একটু অপেক্ষা করো, আমি ভাবছি...")

        headers = {
            "Authorization": f"Bearer {OPENROUTER_API_KEY}",
            "Content-Type": "application/json"
        }
        payload = {
            "model": "openai/gpt-3.5-turbo",
            "messages": [{"role": "user", "content": msg}],
            "temperature": 0.7
        }

        res = requests.post("https://openrouter.ai/api/v1/chat/completions", headers=headers, json=payload)

        if res.status_code == 200:
            reply = res.json()['choices'][0]['message']['content']
            await update.message.reply_text(reply)
        else:
            await update.message.reply_text("দুঃখিত, উত্তর আনতে সমস্যা হচ্ছে 😔")

# App setup
app = ApplicationBuilder().token(BOT_TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, chat))

# Run
if __name__ == "__main__":
    print("EvuBot চালু হয়েছে ✅")
    app.run_polling()
