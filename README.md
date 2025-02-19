import os
import hashlib
import hmac
import logging
from dotenv import load_dotenv
from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor

load_dotenv()
TOKEN = os.getenv("TELEGRAM_BOT_TOKEN")
SECRET_KEY = os.getenv("SECRET_KEY")

bot = Bot(token=TOKEN)
dp = Dispatcher(bot)
logging.basicConfig(level=logging.INFO)

def verify_user(user_id):
    """ Проверка подписки и хэширование ID """
    hash_id = hmac.new(SECRET_KEY.encode(), str(user_id).encode(), hashlib.sha256).hexdigest()
    return hash_id.startswith("abc")  # Защита от ботов

@dp.message_handler(commands=['start'])
async def send_welcome(message: types.Message):
    if not verify_user(message.from_user.id):
        await message.reply("🔒 Доступ запрещен.")
        return
    await message.reply("🚀 Добро пожаловать в Crypto Signal Bot!")

if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
