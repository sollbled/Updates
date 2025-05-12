# Updates
import discord
from discord.ext import commands
import re

# إعداد البوت
intents = discord.Intents.default()
intents.message_content = True
intents.members = True
bot = commands.Bot(command_prefix='!', intents=intents)

# قائمة الكلمات البذيئة
banned_words = ['badword1', 'badword2', 'badword3']  # ضع الكلمات التي تريد حظرها هنا

# إضافة مراقبة الرسائل
@bot.event
async def on_message(message):
    # تجاهل الرسائل التي يرسلها البوت نفسه
    if message.author == bot.user:
        return

    # فحص الرسائل عن وجود كلمات بذيئة
    if any(word in message.content.lower() for word in banned_words):
        await message.delete()
        await message.channel.send(f'{message.author.mention} تم حذف رسالتك بسبب كلمات غير لائقة!')
    
    await bot.process_commands(message)

# إضافة حماية ضد البوتات
@bot.event
async def on_member_join(member):
    # فحص إذا كان العضو الجديد بوتًا
    if member.bot:
        await member.kick(reason="Bots are not allowed.")
        await member.guild.system_channel.send(f'{member.name} تم طرده لأنه بوت.')

# إضافة أمر لتحقق العضو
@bot.command()
async def verify(ctx):
    """تفعيل التحقق اليدوي (يمكنك تخصيصه حسب حاجتك)"""
    await ctx.send("أرسل كلمة السر لتتمكن من التحقق!")

# إضافة إدارة الأعضاء
@bot.command()
async def ban(ctx, user: discord.User, reason=None):
    """حظر عضو"""
    await user.ban(reason=reason)
    await ctx.send(f"{user} تم حظره بسبب: {reason}")

@bot.command()
async def kick(ctx, user: discord.User, reason=None):
    """طرد عضو"""
    await user.kick(reason=reason)
    await ctx.send(f"{user} تم طرده بسبب: {reason}")

# بدء البوت
@bot.event
async def on_ready():
    print(f'بوت الحماية {bot.user} جاهز الآن للعمل!')

