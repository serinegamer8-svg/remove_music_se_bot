import telebot
import subprocess
import os

# ضعي هنا التوكن من BotFather
TOKEN = "ضع_رمز_البوت_هنا"

bot = telebot.TeleBot(TOKEN)

@bot.message_handler(content_types=['video', 'video_note'])
def handle_video(message):
    file_info = bot.get_file(message.video.file_id)
    downloaded_file = bot.download_file(file_info.file_path)
    input_path = "input.mp4"
    output_path = "output.mp4"

    # نحفظ الفيديو المرسل
    with open(input_path, 'wb') as new_file:
        new_file.write(downloaded_file)

    # نحاول إزالة الموسيقى (نخفض الصوت الموسيقي)
    # هذا يستخدم ffmpeg لتقليل الأصوات الموسيقية البحتة
    command = f'ffmpeg -i {input_path} -af "arnndn=m=model.nrn" -c:v copy {output_path}'
    subprocess.run(command, shell=True)

    # نرسل الفيديو الناتج
    with open(output_path, 'rb') as video:
        bot.send_video(message.chat.id, video)

    # نحذف الملفات بعد الإرسال
    os.remove(input_path)
    os.remove(output_path)

bot.polling()
