# Bot-mahkamah
Discord bot for my servers

# bot.py
import discord
from discord.ext import commands
import asyncio
import random

# ====== CONFIG ======
TOKEN = "YOUR_BOT_TOKEN"  # <-- gantikan di sini dengan token bot awak
TOTAL_KES = 1000
# ====================

intents = discord.Intents.all()
bot = commands.Bot(command_prefix="!", intents=intents)

sesi_mahkamah = {}  # keyed by channel.id

# --------------------
# Fungsi menjana 1000 kes secara automatik
# --------------------
def generate_cases(total=1000):
    categories = ["jenayah", "rasuah", "sivil", "keluarga"]

    names_first = ["Ahmad","Siti","Muhammad","Aisyah","Rahman","Nurul","Ali","Zainab","Hassan","Farah","Amir","Lina"]
    names_last = ["bin Ali","binti Abu","bin Omar","binti Rahim","bin Ismail","binti Hassan","Tan","Lim","Singh","Smith"]
    locations = ["Kuala Lumpur","Johor Bahru","Kuantan","Kota Kinabalu","Kuching","Ipoh","Alor Setar","Seremban","Melaka","Putrajaya"]
    companies = ["Syarikat Maju Sdn Bhd", "Industri Gemilang Sdn Bhd", "Perkhidmatan Selaras Sdn Bhd", "Kilang Timur Sdn Bhd", "TeknoSdnBhd"]
    weapons = ["pisau", "senjata api", "palu", "botol kaca", "tali", "pemukul"]

    jenayah_types = ["Rompakan", "Pembunuhan", "Pencurian Kenderaan", "Pengedaran Dadah", "Serangan"]
    rasuah_types = ["Rasuah Pegawai", "Sogokan Kontraktor", "Penyelewengan Dana", "Penyalahgunaan Kuasa"]
    sivil_types = ["Pertikaian Kontrak", "Tuntutan Ganti Rugi", "Pencemaran Alam", "Pemecatan Tidak Sah"]
    keluarga_types = ["Hak Penjagaan Anak", "Perceraian & Harta", "Fitnah Media Sosial", "Pertikaian Penjagaan"]

    per_cat = total // len(categories)
    remainder = total % len(categories)

    db = {cat: [] for cat in categories}

    def rand_name():
        return f"{random.choice(names_first)} {random.choice(names_last)}"

    def rand_location():
        return random.choice(locations)

    def money():
        return f"RM{random.randint(1_000, 5_000_000):,}"

    for cat in categories:
        count = per_cat + (1 if remainder > 0 else 0)
        if remainder > 0:
            remainder -= 1
        for i in range(count):
            year = random.randint(2017, 2024)
            name = rand_name()
            loc = rand_location()
            comp = random.choice(companies)
            weapon = random.choice(weapons)
            amt = money()

            if cat == "jenayah":
                subtype = random.choice(jenayah_types)
                tajuk = f"Kes {subtype} di {loc} ({i+1})"
                if subtype == "Rompakan":
                    ringkasan = (f"Tertuduh {name} didakwa merompak sebuah premis di {loc} pada tahun {year} "
                                 f"dengan menggunakan {weapon}. Pihak pembela menegaskan tertuduh hanya berada di lokasi sebagai pelanggan.")
                elif subtype == "Pembunuhan":
                    ringkasan = (f"Tertuduh {name} didakwa membunuh seorang jirannya selepas pertengkaran di {loc} pada {year}. "
                                 "Pembelaan mendakwa ia adalah mempertahankan diri.")
                elif subtype == "Pencurian Kenderaan":
                    ringkasan = (f"Tertuduh {name} ditangkap bersama sebuah kenderaan mewah yang disyaki dicuri. "
                                 f"Tertuduh mendakwa kenderaan itu dipinjam daripada rakan pada tahun {year}.")
                elif subtype == "Pengedaran Dadah":
                    ringkasan = (f"Tertuduh {name} disyaki terlibat pengedaran dadah di sekitar {loc}. Bukti termasuk rekod transaksi dan saksi.")
                else:
                    ringkasan = (f"Kes jenayah melibatkan {name} di {loc}. Perincian menuntut pembuktian niat dan bukti forensik.")
            elif cat == "rasuah":
                subtype = random.choice(rasuah_types)
                tajuk = f"Kes {subtype} ({i+1})"
                if subtype == "Rasuah Pegawai":
                    ringkasan = (f"Seorang pegawai (tertuduh {name}) dituduh menerima {amt} untuk meluluskan projek. "
                                 f"Peguam bela mendakwa ia adalah sumbangan politik yang sah.")
                elif subtype == "Sogokan Kontraktor":
                    ringkasan = (f"Tertuduh dari {comp} didakwa memberi sogokan kepada pegawai untuk mendapatkan kontrak bernilai {amt}.")
                elif subtype == "Penyelewengan Dana":
                    ringkasan = (f"Tertuduh {name} dituduh memindahkan dana syarikat secara haram. Bukti transaksi dipertikaikan.")
                else:
                    ringkasan = (f"Kes berkaitan salah guna kuasa dan rasuah melibatkan pegawai dan kontraktor di {loc}.")
            elif cat == "sivil":
                subtype = random.choice(sivil_types)
                tajuk = f"Kes {subtype} - {comp} ({i+1})"
                if subtype == "Pertikaian Kontrak":
                    ringkasan = (f"Syarikat menuntut ganti rugi kerana kontrak gagal dilaksanakan. Syarikat defend menafikan liabiliti.")
                elif subtype == "Tuntutan Ganti Rugi":
                    ringkasan = (f"Pihak plaintif menuntut {amt} daripada defend akibat kegagalan perkhidmatan oleh {comp}.")
                elif subtype == "Pencemaran Alam":
                    ringkasan = (f"Penduduk mendakwa pencemaran sungai oleh {comp}. Bukti saintifik dipertikaikan.")
                else:
                    ringkasan = (f"Pertikaian sivil antara dua entiti berkaitan pelanggaran kontrak dan ganti rugi.")
            else:  # keluarga
                subtype = random.choice(keluarga_types)
                tajuk = f"Kes {subtype} ({i+1})"
                if subtype == "Hak Penjagaan Anak":
                    ringkasan = (f"Selepas perceraian, ibu/bapa menuntut hak penjagaan anak. Kedua pihak memberi alasan terbaik untuk anak.")
                elif subtype == "Perceraian & Harta":
                    ringkasan = (f"Perbalahan tentang pembahagian harta selepas perceraian. Sifat harta dan sumbangan dinilai.")
                elif subtype == "Fitnah Media Sosial":
                    ringkasan = (f"Individu mendakwa fitnah melalui media sosial yang menjejaskan reputasi. Bukti posting dan niat dipertikaikan.")
                else:
                    ringkasan = (f"Isu keluarga berkaitan penjagaan, nafkah atau fitnah yang perlu keputusan mahkamah.")
            db[cat].append({"tajuk": tajuk, "ringkasan": ringkasan})
    return db

# Generate cases DB (1000 kes)
KES_DB = generate_cases(TOTAL_KES)

# --------------------
# Bot events & commands
# --------------------
@bot.event
async def on_ready():
    print(f"🤖 Bot telah online sebagai {bot.user}")
    # Log ringkasan
    counts = {k: len(v) for k, v in KES_DB.items()}
    print("Bilangan kes per kategori:", counts)

# Helper: check author is hakim for that channel
def is_hakim(ctx, mahkamah):
    return ctx.author == mahkamah.get("hakim")

# !senarai_kes [kategori] [jumlah]
@bot.command()
async def senarai_kes(ctx, kategori: str = None, jumlah: int = 10):
    if kategori is None:
        # paparkan ringkasan kategori & bilangan
        lines = ["📂 **Senarai Kes Tersedia (ringkasan):**"]
        for k, v in KES_DB.items():
            lines.append(f"🔹 **{k.capitalize()}** — {len(v)} kes")
        await ctx.send("\n".join(lines))
        return

    k = kategori.lower()
    if k not in KES_DB and k != "semua":
        return await ctx.send("⚠️ Kategori tidak sah. Gunakan: `jenayah`, `rasuah`, `sivil`, `keluarga`, atau `semua`.")
    jumlah = max(1, min(jumlah, 20))  # hadkan untuk elak mesej terlalu panjang

    to_list = []
    if k == "semua":
        # gabungkan semua, tapi hanya senaraikan jumlah terawal
        all_cases = sum(KES_DB.values(), [])
        for idx, kes in enumerate(all_cases[:jumlah], start=1):
            to_list.append(f"{idx}. {kes['tajuk']}")
    else:
        for idx, kes in enumerate(KES_DB[k][:jumlah], start=1):
            to_list.append(f"{idx}. {kes['tajuk']}")

    header = f"📂 Senarai kes untuk **{k if k != 'semua' else 'semua kategori'}** (menunjukkan {len(to_list)}):"
    await ctx.send(header + "\n" + "\n".join(to_list))

# !mula_bersidang <kategori> @hakim @bela @raya @tertuduh @saksi
@bot.command()
async def mula_bersidang(ctx, kategori: str, hakim: discord.Member, bela: discord.Member, raya: discord.Member, tertuduh: discord.Member, saksi: discord.Member):
    kategori = kategori.lower()
    if kategori == "semua":
        semua = sum(KES_DB.values(), [])
        kes = random.choice(semua)
    elif kategori in KES_DB:
        kes = random.choice(KES_DB[kategori])
    else:
        return await ctx.send("⚠️ Kategori tidak sah. Gunakan: `jenayah`, `rasuah`, `sivil`, `keluarga`, atau `semua`.")

    sesi_mahkamah[ctx.channel.id] = {
        "hakim": hakim,
        "bela": bela,
        "raya": raya,
        "teruduh": tertuduh,
        "saksi": saksi,
        "status": "berjalan",
        "fase": "hujah",   # hujah -> rumusan
        "turn": 0,         # 0 -> raya, 1 -> bela
        "kes": kes,
        "timer_active": False,
        "last_undi_result": None
    }

    await ctx.send(
        f"🧑‍⚖️ **Sesi Mahkamah Bermula!**\n"
        f"Hakim: {hakim.mention}\n"
        f"Peguam Bela: {bela.mention}\n"
        f"Peguam Raya: {raya.mention}\n"
        f"Tertuduh: {tertuduh.mention}\n"
        f"Saksi: {saksi.mention}\n\n"
        f"📂 **Kes Diberikan:** **{kes['tajuk']}**\n"
        f"📜 **Ringkasan:** {kes['ringkasan']}\n\n"
        f"👉 Gunakan `!giliran` (oleh Hakim) untuk mula hujah. (Setiap peguam 5 minit; rumusan 2 minit.)"
    )

# Internal helper: run timer with basic reminders
async def _run_timer(ctx, mahkamah, role_key, masa, mesej_masa, giliran_list):
    member = mahkamah[role_key]
    mahkamah["timer_active"] = True
    await ctx.send(f"🎙️ Sekarang giliran: {member.mention} — **{mesej_masa}**. Sila mulakan hujah.")
    # Reminders: jika masa besar, berikan peringatan 60s & 10s (atau 30s untuk rumusan)
    try:
        if masa > 60:
            # tunggu hingga 60s tinggal
            await asyncio.sleep(masa - 60)
            await ctx.send(f"⏳ 1 minit tinggal untuk {member.mention}")
            await asyncio.sleep(40)
            await ctx.send("⏲️ 20 saat tinggal!")
            await asyncio.sleep(20)
        elif masa > 30:
            await asyncio.sleep(masa - 30)
            await ctx.send(f"⏳ 30 saat tinggal untuk {member.mention}")
            await asyncio.sleep(20)
            await ctx.send("⏲️ 10 saat tinggal!")
            await asyncio.sleep(10)
        else:
            await asyncio.sleep(masa)
    except asyncio.CancelledError:
        await ctx.send("⚠️ Timer dibatalkan.")
        mahkamah["timer_active"] = False
        raise
    # tamat
    await ctx.send(f"⏰ Masa **{mesej_masa}** untuk {member.mention} telah tamat!")
    # update turn
    mahkamah["turn"] = (mahkamah["turn"] + 1) % len(giliran_list)
    mahkamah["timer_active"] = False

    # phase transitions
    if mahkamah["fase"] == "hujah" and mahkamah["turn"] == 0:
        mahkamah["fase"] = "rumusan"
        await ctx.send("📌 Semua hujah selesai. Kini masuk fasa **RUMUSAN** (2 minit setiap peguam). Gunakan `!giliran` untuk mula rumusan.")
    elif mahkamah["fase"] == "rumusan" and mahkamah["turn"] == 0:
        await ctx.send("✅ Semua hujah & rumusan selesai. Hakim boleh buka `!undi` atau gunakan `!putusan` untuk umum keputusan.")

# !giliran (oleh hakim sahaja)
@bot.command()
async def giliran(ctx):
    mahkamah = sesi_mahkamah.get(ctx.channel.id)
    if not mahkamah:
        return await ctx.send("⚠️ Tiada sesi mahkamah sedang berlangsung di channel ini.")
    if not is_hakim(ctx, mahkamah):
        return await ctx.send("❌ Hanya Hakim yang boleh mengawal giliran (gunakan akaun Hakim yang ditetapkan).")

    if mahkamah.get("timer_active"):
        return await ctx.send("⏳ Timer sedang berjalan. Sila tunggu sehingga masa tamat sebelum memulakan giliran seterusnya.")

    if mahkamah["fase"] == "hujah":
        giliran_list = ["raya", "bela"]
        masa = 300  # 5 minit
        mesej_masa = "5 minit berhujah"
    else:  # rumusan
        giliran_list = ["raya", "bela"]
        masa = 120  # 2 minit rumusan
        mesej_masa = "2 minit rumusan"

    role_key = giliran_list[mahkamah["turn"]]
    # jalankan timer (tidak memblok event loop untuk command lain)
    bot.loop.create_task(_run_timer(ctx, mahkamah, role_key, masa, mesej_masa, giliran_list))

# !undi [tempoh]
@bot.command()
async def undi(ctx, tempoh: int = 30):
    mahkamah = sesi_mahkamah.get(ctx.channel.id)
    if not mahkamah:
        return await ctx.send("⚠️ Tiada sesi mahkamah sedang berlangsung.")
    if not is_hakim(ctx, mahkamah):
        return await ctx.send("❌ Hanya Hakim boleh membuka undi.")

    msg = await ctx.send(
        f"⚖️ **Undian keputusan bermula selama {tempoh} saat!**\n"
        f"👍 = Bersalah\n"
        f"👎 = Tidak Bersalah"
    )
    await msg.add_reaction("👍")
    await msg.add_reaction("👎")
    # tunggu tempoh
    await asyncio.sleep(tempoh)

    # kira
    msg = await ctx.channel.fetch_message(msg.id)
    undi_bersalah = 0
    undi_tidak = 0
    for reaction in msg.reactions:
        if reaction.emoji == "👍":
            undi_bersalah = max(0, reaction.count - 1)
        elif reaction.emoji == "👎":
            undi_tidak = max(0, reaction.count - 1)

    if undi_bersalah > undi_tidak:
        keputusan = "BERSALAH ⚖️"
    elif undi_tidak > undi_bersalah:
        keputusan = "TIDAK BERSALAH 🕊️"
    else:
        keputusan = "SERII! ⚖️"

    mahkamah["last_undi_result"] = {"bersalah": undi_bersalah, "tidak": undi_tidak, "keputusan": keputusan}

    await ctx.send(
        f"📊 **Keputusan Undian:**\n"
        f"👍 Bersalah: {undi_bersalah}\n"
        f"👎 Tidak Bersalah: {undi_tidak}\n"
        f"🏆 Keputusan akhir (undi): **{keputusan}**\n"
        f"Nota: Hakim boleh gunakan `!putusan <bersalah|tidak|seri>` untuk override jika perlu."
    )

# !putusan <bersalah|tidak|seri>  -- hakim override
@bot.command()
async def keputusan(ctx, hasil: str):
    mahkamah = sesi_mahkamah.get(ctx.channel.id)
    if not mahkamah:
        return await ctx.send("⚠️ Tiada sesi mahkamah sedang berlangsung.")
    if not is_hakim(ctx, mahkamah):
        return await ctx.send("❌ Hanya Hakim boleh buat keputusan akhir.")

    hasil = hasil.lower()
    if hasil in ("bersalah", "bersalah."):
        teks = "BERSALAH ⚖️"
    elif hasil in ("tidak", "tidakbersalah", "tidak_bersalah", "tidak bersalah", "tidak."):
        teks = "TIDAK BERSALAH 🕊️"
    elif hasil in ("seri", "seri."):
        teks = "SERII! ⚖️"
    else:
        return await ctx.send("⚠️ Gunakan: `!putusan bersalah` atau `!putusan tidak` atau `!putusan seri`.")

    await ctx.send(f"🏁 **Keputusan Hakim:** {teks}\n(Keputusan ini adalah autoriti Hakim.)")

# !status
@bot.command()
async def status(ctx):
    mahkamah = sesi_mahkamah.get(ctx.channel.id)
    if not mahkamah:
        return await ctx.send("⚠️ Tiada sesi mahkamah sedang berlangsung.")
    fase = mahkamah["fase"].capitalize()
    turn_text = "Peguam Raya" if mahkamah["turn"] == 0 else "Peguam Bela"
    kes = mahkamah.get("kes", {})
    tajuk = kes.get("tajuk", "Tiada")
    await ctx.send(f"📌 Status: Fasa **{fase}**\n🔹 Kes: **{tajuk}**\n🔹 Giliran seterusnya: **{turn_text}**\n🔹 Timer aktif: {mahkamah.get('timer_active', False)}")

# !tamat_bersidang
@bot.command()
async def tamat_bersidang(ctx):
    mahkamah = sesi_mahkamah.pop(ctx.channel.id, None)
    if not mahkamah:
        return await ctx.send("⚠️ Tiada sesi mahkamah sedang berlangsung.")
    await ctx.send("🛑 Sesi mahkamah telah ditamatkan!")

# Jalankan bot
if __name__ == "__main__":
    bot.run(TOKEN)
