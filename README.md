یوتوب دانلودر
import telebot
import yt_dlp
import os

# توکن رباتت رو اینجا بذار 👇
BOT_TOKEN = "8463234347:AAHTFpOtaCxke4vtR-ABTu86rMy4Bc05U-8"

bot = telebot.TeleBot(BOT_TOKEN)

@bot.message_handler(commands=['start'])
def send_welcome(message):
    bot.reply_to(message, "سلام! 👋 لینک ویدیوی یوتیوب یا Shorts رو بفرست تا دانلودش کنم 🎬")

@bot.message_handler(func=lambda message: True)
def handle_text(message):
    url = message.text.strip()

    if not ("youtube.com" in url or "youtu.be" in url):
        bot.reply_to(message, "❌ لطفاً فقط لینک یوتیوب یا شورت بفرست.")
        return

    bot.reply_to(message, "📥 در حال دانلود ویدیو، لطفاً صبر کن...")

    try:
        ydl_opts = {
            'format': 'mp4[height<=480]',  # فقط ویدیو، بدون نیاز به ffmpeg
            'outtmpl': 'downloaded_video.%(ext)s',
            'quiet': True,
        }

        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=True)
            filename = ydl.prepare_filename(info)

        # ارسال ویدیو برای کاربر
        with open(filename, 'rb') as video:
            bot.send_video(message.chat.id, video, caption="✅ دانلود با موفقیت انجام شد!")

        os.remove(filename)

    except Exception as e:
        bot.reply_to(message, f"⚠️ خطا در پردازش لینک:\n{e}")

print("Bot started...")
bot.infinity_polling()


