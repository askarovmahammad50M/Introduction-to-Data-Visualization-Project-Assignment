FoodRadar AI - Ollama Destekli Yemek Fiyat Karşılaştırma ve Görselleştirme Uygulaması

Kullanım:
1) Ollama açık olmalı.
2) Terminalde örnek model indirilebilir:
   ollama pull gemma3:1b
3) BASLAT.bat çalıştırılır.
4) İstediğin yerde yemek adını seçip F8'e basabilirsin.
5) Uygulama açılıp yemeği Trendyol Go, Yemeksepeti ve GetirYemek arasında ucuzdan pahalıya sıralar.

Bu uygulama eğitim/proje amaçlıdır. Gerçek uygulama API'lerine bağlanmaz.
"""

import json
import math
import random
import threading
import time
import queue
import tkinter as tk
from tkinter import ttk, messagebox

try:
    import requests
except Exception:
    requests = None

try:
    import pyperclip
except Exception:
    pyperclip = None

try:
    import pyautogui
except Exception:
    pyautogui = None

try:
    from pynput import keyboard
except Exception:
    keyboard = None


# ============================================================
# AYARLAR
# ============================================================

APP_NAME = "FoodRadar AI"
APP_SUBTITLE = "Ollama Destekli Yemek Fiyat Karşılaştırma ve Veri Görselleştirme"
OLLAMA_URL = "http://localhost:11434/api/generate"
OLLAMA_TAGS_URL = "http://localhost:11434/api/tags"

MODEL_CANDIDATES = [
    "gemma3:1b",
    "llama3.2",
    "mistral",
    "qwen2.5:3b",
    "gemma3",
]

HOTKEY = keyboard.Key.f8 if keyboard else None

PLATFORMS = ["Trendyol Go", "Yemeksepeti", "GetirYemek"]

THEME = {
    "bg": "#080B12",
    "panel": "#111827",
    "panel_2": "#172033",
    "card": "#1F2937",
    "card_2": "#263244",
    "text": "#F8FAFC",
    "muted": "#94A3B8",
    "green": "#22C55E",
    "yellow": "#FACC15",
    "red": "#FB7185",
    "blue": "#38BDF8",
    "purple": "#A78BFA",
    "line": "#334155",
    "white": "#FFFFFF",
}


# ============================================================
# DEMO VERİ SETİ
# ============================================================

FOOD_DATABASE = {
    "lahmacun": {
        "category": "Türk Mutfağı",
        "keywords": ["lahmacun", "lahmacun menü", "antep lahmacun"],
        "base": {
            "Trendyol Go": (95, 128),
            "Yemeksepeti": (89, 135),
            "GetirYemek": (92, 132),
        },
    },
    "pizza": {
        "category": "Fast Food",
        "keywords": ["pizza", "karışık pizza", "orta boy pizza", "pepperoni pizza"],
        "base": {
            "Trendyol Go": (175, 245),
            "Yemeksepeti": (169, 255),
            "GetirYemek": (180, 260),
        },
    },
    "hamburger": {
        "category": "Fast Food",
        "keywords": ["hamburger", "burger", "cheeseburger", "menü"],
        "base": {
            "Trendyol Go": (145, 225),
            "Yemeksepeti": (139, 235),
            "GetirYemek": (149, 240),
        },
    },
    "döner": {
        "category": "Türk Mutfağı",
        "keywords": ["döner", "tavuk döner", "et döner", "dürüm"],
        "base": {
            "Trendyol Go": (110, 180),
            "Yemeksepeti": (105, 190),
            "GetirYemek": (115, 195),
        },
    },
    "tavuk döner": {
        "category": "Türk Mutfağı",
        "keywords": ["tavuk döner", "tavuk dürüm", "döner"],
        "base": {
            "Trendyol Go": (95, 155),
            "Yemeksepeti": (90, 165),
            "GetirYemek": (98, 168),
        },
    },
    "et döner": {
        "category": "Türk Mutfağı",
        "keywords": ["et döner", "et dürüm", "döner"],
        "base": {
            "Trendyol Go": (165, 250),
            "Yemeksepeti": (155, 260),
            "GetirYemek": (170, 265),
        },
    },
    "çiğ köfte": {
        "category": "Ekonomik / Atıştırmalık",
        "keywords": ["çiğ köfte", "çiğköfte", "dürüm çiğ köfte"],
        "base": {
            "Trendyol Go": (55, 95),
            "Yemeksepeti": (50, 100),
            "GetirYemek": (58, 105),
        },
    },
    "mantı": {
        "category": "Ev Yemeği",
        "keywords": ["mantı", "kayseri mantısı"],
        "base": {
            "Trendyol Go": (135, 205),
            "Yemeksepeti": (129, 215),
            "GetirYemek": (140, 220),
        },
    },
    "kebap": {
        "category": "Türk Mutfağı",
        "keywords": ["kebap", "adana kebap", "urfa kebap"],
        "base": {
            "Trendyol Go": (190, 320),
            "Yemeksepeti": (185, 330),
            "GetirYemek": (195, 345),
        },
    },
    "pide": {
        "category": "Türk Mutfağı",
        "keywords": ["pide", "kıymalı pide", "kuşbaşılı pide"],
        "base": {
            "Trendyol Go": (120, 205),
            "Yemeksepeti": (115, 215),
            "GetirYemek": (125, 220),
        },
    },
    "sushi": {
        "category": "Dünya Mutfağı",
        "keywords": ["sushi", "roll", "california roll"],
        "base": {
            "Trendyol Go": (260, 460),
            "Yemeksepeti": (245, 480),
            "GetirYemek": (270, 500),
        },
    },
    "makarna": {
        "category": "İtalyan Mutfağı",
        "keywords": ["makarna", "pasta", "alfredo", "napoliten"],
        "base": {
            "Trendyol Go": (125, 210),
            "Yemeksepeti": (119, 220),
            "GetirYemek": (130, 230),
        },
    },
    "tost": {
        "category": "Kahvaltı / Pratik",
        "keywords": ["tost", "karışık tost", "kaşarlı tost"],
        "base": {
            "Trendyol Go": (65, 120),
            "Yemeksepeti": (60, 125),
            "GetirYemek": (68, 130),
        },
    },
    "köfte": {
        "category": "Türk Mutfağı",
        "keywords": ["köfte", "ızgara köfte", "köfte menü"],
        "base": {
            "Trendyol Go": (135, 230),
            "Yemeksepeti": (130, 240),
            "GetirYemek": (140, 245),
        },
    },
    "baklava": {
        "category": "Tatlı",
        "keywords": ["baklava", "fıstıklı baklava", "tatlı"],
        "base": {
            "Trendyol Go": (180, 330),
            "Yemeksepeti": (175, 350),
            "GetirYemek": (190, 360),
        },
    },
}


# ============================================================
# YARDIMCI FONKSİYONLAR
# ============================================================

def normalize_text(text):
    text = (text or "").strip().lower()
    replacements = {
        "ı": "i",
        "ğ": "g",
        "ü": "u",
        "ş": "s",
        "ö": "o",
        "ç": "c",
        "İ": "i",
    }
    for old, new in replacements.items():
        text = text.replace(old, new)
    return " ".join(text.split())


def money(value):
    return f"{value:,.2f} TL".replace(",", "X").replace(".", ",").replace("X", ".")


def stable_seed(text):
    return sum(ord(ch) for ch in text) + len(text) * 97


def find_food_key(query):
    q_norm = normalize_text(query)

    if not q_norm:
        return None

    best_key = None
    best_score = 0

    for food_key, data in FOOD_DATABASE.items():
        candidates = [food_key] + data["keywords"]
        for candidate in candidates:
            c_norm = normalize_text(candidate)

            score = 0
            if q_norm == c_norm:
                score = 100
            elif q_norm in c_norm or c_norm in q_norm:
                score = 75
            else:
                q_words = set(q_norm.split())
                c_words = set(c_norm.split())
                if q_words and c_words:
                    score = len(q_words & c_words) * 25

            if score > best_score:
                best_score = score
                best_key = food_key

    if best_score >= 25:
        return best_key

    return None


def build_synthetic_price_data(food_query):
    """
    Eğitim/proje amaçlı deterministik demo veri üretimi.
    Aynı yemek adı aynı sonuçlara yakın fiyatlar üretir.
    """
    food_key = find_food_key(food_query)
    if not food_key:
        food_key = "hamburger"

    food_data = FOOD_DATABASE[food_key]
    seed = stable_seed(food_query + food_key)
    rnd = random.Random(seed)

    rows = []
    campaign_names = [
        "Sepette indirim",
        "Kuponlu fiyat",
        "Öğrenci fırsatı",
        "Hızlı teslimat",
        "Popüler restoran",
        "Akşam indirimi",
    ]

    for platform in PLATFORMS:
        low, high = food_data["base"][platform]
        product_price = rnd.randint(low, high)

        service_fee = rnd.choice([0, 4.99, 6.99, 7.99, 9.99, 12.99])
        delivery_fee = rnd.choice([0, 9.99, 14.99, 19.99, 24.99, 29.99])
        discount = rnd.choice([0, 5, 10, 15, 20, 25, 30])

        subtotal = product_price + service_fee + delivery_fee
        total = max(product_price * 0.72, subtotal - discount)

        rating = round(rnd.uniform(4.1, 4.9), 1)
        delivery_min = rnd.randint(18, 45)
        restaurant_count = rnd.randint(8, 42)

        rows.append({
            "platform": platform,
            "food": food_key.title(),
            "category": food_data["category"],
            "product_price": float(product_price),
            "service_fee": float(service_fee),
            "delivery_fee": float(delivery_fee),
            "discount": float(discount),
            "total": round(float(total), 2),
            "rating": rating,
            "delivery_min": delivery_min,
            "restaurant_count": restaurant_count,
            "campaign": rnd.choice(campaign_names),
        })

    rows.sort(key=lambda x: x["total"])

    cheapest = rows[0]["total"]
    for index, row in enumerate(rows, start=1):
        row["rank"] = index
        row["difference"] = round(row["total"] - cheapest, 2)

    return food_key, rows


def calculate_project_metrics(rows):
    totals = [r["total"] for r in rows]
    avg_price = sum(totals) / len(totals)
    min_price = min(totals)
    max_price = max(totals)
    spread = max_price - min_price
    spread_percent = (spread / min_price) * 100 if min_price else 0

    return {
        "avg_price": avg_price,
        "min_price": min_price,
        "max_price": max_price,
        "spread": spread,
        "spread_percent": spread_percent,
    }


def get_available_ollama_model():
    if requests is None:
        return None

    try:
        response = requests.get(OLLAMA_TAGS_URL, timeout=3)
        if response.status_code != 200:
            return None

        models = response.json().get("models", [])
        installed_names = [m.get("name", "") for m in models]

        for candidate in MODEL_CANDIDATES:
            for installed in installed_names:
                if installed.lower() == candidate.lower():
                    return installed

        if installed_names:
            return installed_names[0]

    except Exception:
        return None

    return None


def ask_ollama(food_name, rows):
    if requests is None:
        return "requests paketi bulunamadığı için AI yorumu alınamadı."

    model = get_available_ollama_model()
    if not model:
        return (
            "Ollama bağlantısı bulunamadı. Program veri analizi kısmını çalıştırdı; "
            "AI yorum için Ollama açık olmalı ve en az bir model kurulu olmalıdır."
        )

    compact_data = []
    for row in rows:
        compact_data.append({
            "uygulama": row["platform"],
            "urun_fiyati": row["product_price"],
            "teslimat": row["delivery_fee"],
            "hizmet": row["service_fee"],
            "indirim": row["discount"],
            "toplam": row["total"],
            "puan": row["rating"],
            "sure": row["delivery_min"],
        })

    prompt = f"""
Sen bir yemek fiyat karşılaştırma uygulamasının AI analiz modülüsün.
Kullanıcı "{food_name}" yemeğini aradı.

Veri:
{json.dumps(compact_data, ensure_ascii=False, indent=2)}

Görev:
- En ucuz uygulamayı söyle.
- Fiyat farkını yorumla.
- Sadece ucuzluğu değil teslimat süresi ve restoran puanını da dikkate al.
- Öğrenci/proje sunumu için profesyonel ama kısa bir analiz yaz.
- Cevabı Türkçe ver.
- 5 maddeden uzun olmasın.
"""

    payload = {
        "model": model,
        "prompt": prompt,
        "stream": False,
        "options": {
            "temperature": 0.45,
            "top_p": 0.9,
        }
    }

    try:
        response = requests.post(OLLAMA_URL, json=payload, timeout=45)
        if response.status_code == 200:
            data = response.json()
            return data.get("response", "").strip() or "AI boş cevap döndürdü."
        return f"Ollama API hatası: HTTP {response.status_code}"
    except Exception as exc:
        return f"Ollama bağlantı hatası: {exc}"


def copy_selected_text():
    if pyperclip is None or pyautogui is None:
        return ""

    marker = f"__FOODRADAR_MARKER_{time.time_ns()}__"

    try:
        pyperclip.copy(marker)
        time.sleep(0.05)
        pyautogui.hotkey("ctrl", "c")
        time.sleep(0.25)
        value = pyperclip.paste()
        if value and value.strip() and value != marker:
            return value.strip()
    except Exception:
        return ""

    return ""


# ============================================================
# TKINTER UI
# ============================================================

class FoodRadarApp:
    def __init__(self, root):
        self.root = root
        self.root.title(APP_NAME)
        self.root.geometry("1180x760")
        self.root.minsize(980, 660)
        self.root.configure(bg=THEME["bg"])

        self.queue = queue.Queue()
        self.current_rows = []
        self.current_food = ""
        self.listener = None
        self.hotkey_locked = False

        self.setup_styles()
        self.build_layout()
        self.root.after(120, self.process_queue)
        self.start_hotkey_listener()
        self.search_var.set("lahmacun")
        self.run_search()

    def setup_styles(self):
        style = ttk.Style()
        try:
            style.theme_use("clam")
        except Exception:
            pass

        style.configure(
            "Premium.Treeview",
            background=THEME["panel"],
            foreground=THEME["text"],
            rowheight=34,
            fieldbackground=THEME["panel"],
            bordercolor=THEME["line"],
            borderwidth=0,
            font=("Segoe UI", 10)
        )
        style.configure(
            "Premium.Treeview.Heading",
            background=THEME["card"],
            foreground=THEME["white"],
            font=("Segoe UI Semibold", 10),
            padding=8
        )
        style.map(
            "Premium.Treeview",
            background=[("selected", THEME["blue"])],
            foreground=[("selected", "#06111F")]
        )

    def build_layout(self):
        self.build_header()
        self.build_body()
        self.build_footer()

    def build_header(self):
        header = tk.Frame(self.root, bg=THEME["bg"])
        header.pack(fill="x", padx=24, pady=(20, 8))

        left = tk.Frame(header, bg=THEME["bg"])
        left.pack(side="left", fill="x", expand=True)

        title = tk.Label(
            left,
            text="🍽️ FoodRadar AI",
            bg=THEME["bg"],
            fg=THEME["text"],
            font=("Segoe UI Black", 28)
        )
        title.pack(anchor="w")

        subtitle = tk.Label(
            left,
            text="Yemeğini yaz, Trendyol Go • Yemeksepeti • GetirYemek arasında ucuzdan pahalıya sırala.",
            bg=THEME["bg"],
            fg=THEME["muted"],
            font=("Segoe UI", 11)
        )
        subtitle.pack(anchor="w", pady=(2, 0))

        right = tk.Frame(header, bg=THEME["bg"])
        right.pack(side="right")

        status = "Ollama: kontrol ediliyor..."
        self.ollama_status_label = tk.Label(
            right,
            text=status,
            bg=THEME["panel"],
            fg=THEME["yellow"],
            font=("Segoe UI Semibold", 10),
            padx=14,
            pady=8
        )
        self.ollama_status_label.pack(anchor="e")

        self.root.after(400, self.refresh_ollama_status)

    def build_body(self):
        body = tk.Frame(self.root, bg=THEME["bg"])
        body.pack(fill="both", expand=True, padx=24, pady=12)

        left_panel = tk.Frame(body, bg=THEME["panel"])
        left_panel.pack(side="left", fill="both", expand=True, padx=(0, 12))

        right_panel = tk.Frame(body, bg=THEME["panel"])
        right_panel.pack(side="right", fill="both", expand=True, padx=(12, 0))

        self.build_search_area(left_panel)
        self.build_table(left_panel)
        self.build_chart(right_panel)
        self.build_ai_panel(right_panel)

    def build_search_area(self, parent):
        box = tk.Frame(parent, bg=THEME["panel"])
        box.pack(fill="x", padx=18, pady=18)

        label = tk.Label(
            box,
            text="Yemek adı",
            bg=THEME["panel"],
            fg=THEME["muted"],
            font=("Segoe UI Semibold", 10)
        )
        label.pack(anchor="w")

        row = tk.Frame(box, bg=THEME["panel"])
        row.pack(fill="x", pady=(8, 0))

        self.search_var = tk.StringVar()

        entry = tk.Entry(
            row,
            textvariable=self.search_var,
            bg=THEME["card"],
            fg=THEME["text"],
            insertbackground=THEME["text"],
            relief="flat",
            font=("Segoe UI", 16),
            width=28
        )
        entry.pack(side="left", fill="x", expand=True, ipady=12)
        entry.bind("<Return>", lambda event: self.run_search())

        search_button = tk.Button(
            row,
            text="Karşılaştır",
            command=self.run_search,
            bg=THEME["blue"],
            fg="#06111F",
            activebackground="#7DD3FC",
            activeforeground="#06111F",
            relief="flat",
            font=("Segoe UI Semibold", 11),
            padx=18,
            pady=12,
            cursor="hand2"
        )
        search_button.pack(side="left", padx=(10, 0))

        ai_button = tk.Button(
            row,
            text="AI Analiz",
            command=self.run_ai_analysis,
            bg=THEME["purple"],
            fg="#111827",
            activebackground="#C4B5FD",
            activeforeground="#111827",
            relief="flat",
            font=("Segoe UI Semibold", 11),
            padx=18,
            pady=12,
            cursor="hand2"
        )
        ai_button.pack(side="left", padx=(10, 0))

        info = tk.Label(
            box,
            text="İpucu: Herhangi bir yerde yemek adını seçip F8'e basarsan FoodRadar otomatik arama yapar.",
            bg=THEME["panel"],
            fg=THEME["muted"],
            font=("Segoe UI", 9)
        )
        info.pack(anchor="w", pady=(10, 0))

    def build_table(self, parent):
        table_card = tk.Frame(parent, bg=THEME["panel"])
        table_card.pack(fill="both", expand=True, padx=18, pady=(0, 18))

        title = tk.Label(
            table_card,
            text="Ucuzdan Pahalıya Sıralama",
            bg=THEME["panel"],
            fg=THEME["text"],
            font=("Segoe UI Semibold", 15)
        )
        title.pack(anchor="w", pady=(0, 10))

        columns = (
            "rank",
            "platform",
            "product_price",
            "delivery_fee",
            "service_fee",
            "discount",
            "total",
            "rating",
            "delivery_min",
        )

        self.tree = ttk.Treeview(
            table_card,
            columns=columns,
            show="headings",
            style="Premium.Treeview"
        )

        headings = {
            "rank": "#",
            "platform": "Uygulama",
            "product_price": "Ürün",
            "delivery_fee": "Teslimat",
            "service_fee": "Hizmet",
            "discount": "İndirim",
            "total": "Toplam",
            "rating": "Puan",
            "delivery_min": "Süre",
        }

        widths = {
            "rank": 44,
            "platform": 130,
            "product_price": 95,
            "delivery_fee": 95,
            "service_fee": 85,
            "discount": 85,
            "total": 110,
            "rating": 70,
            "delivery_min": 70,
        }

        for col in columns:
            self.tree.heading(col, text=headings[col])
            self.tree.column(col, width=widths[col], anchor="center")

        self.tree.pack(fill="both", expand=True)

        self.metric_frame = tk.Frame(parent, bg=THEME["panel"])
        self.metric_frame.pack(fill="x", padx=18, pady=(0, 18))

    def build_chart(self, parent):
        chart_container = tk.Frame(parent, bg=THEME["panel"])
        chart_container.pack(fill="both", expand=True, padx=18, pady=18)

        top = tk.Frame(chart_container, bg=THEME["panel"])
        top.pack(fill="x")

        title = tk.Label(
            top,
            text="Fiyat Görselleştirmesi",
            bg=THEME["panel"],
            fg=THEME["text"],
            font=("Segoe UI Semibold", 15)
        )
        title.pack(side="left", anchor="w")

        self.food_label = tk.Label(
            top,
            text="",
            bg=THEME["panel"],
            fg=THEME["muted"],
            font=("Segoe UI", 10)
        )
        self.food_label.pack(side="right", anchor="e")

        self.chart = tk.Canvas(
            chart_container,
            bg=THEME["panel_2"],
            highlightthickness=0,
            height=310
        )
        self.chart.pack(fill="both", expand=True, pady=(12, 0))
        self.chart.bind("<Configure>", lambda event: self.draw_chart())

    def build_ai_panel(self, parent):
        panel = tk.Frame(parent, bg=THEME["panel"])
        panel.pack(fill="both", expand=True, padx=18, pady=(0, 18))

        title_row = tk.Frame(panel, bg=THEME["panel"])
        title_row.pack(fill="x")

        title = tk.Label(
            title_row,
            text="Ollama AI Yorumu",
            bg=THEME["panel"],
            fg=THEME["text"],
            font=("Segoe UI Semibold", 15)
        )
        title.pack(side="left", anchor="w")

        copy_btn = tk.Button(
            title_row,
            text="Kopyala",
            command=self.copy_ai_text,
            bg=THEME["card_2"],
            fg=THEME["text"],
            activebackground=THEME["line"],
            activeforeground=THEME["text"],
            relief="flat",
            font=("Segoe UI Semibold", 9),
            padx=10,
            pady=5,
            cursor="hand2"
        )
        copy_btn.pack(side="right")

        self.ai_text = tk.Text(
            panel,
            bg=THEME["panel_2"],
            fg=THEME["text"],
            insertbackground=THEME["text"],
            relief="flat",
            wrap="word",
            height=9,
            font=("Segoe UI", 10),
            padx=12,
            pady=12
        )
        self.ai_text.pack(fill="both", expand=True, pady=(12, 0))
        self.set_ai_text(
            "AI analiz hazır.\n\n"
            "Bir yemek adı girip 'AI Analiz' butonuna basabilirsin. "
            "Ollama kapalıysa uygulama yine fiyat karşılaştırmasını yapar; sadece AI yorumu alınamaz."
        )

    def build_footer(self):
        footer = tk.Frame(self.root, bg=THEME["bg"])
        footer.pack(fill="x", padx=24, pady=(0, 18))

        label = tk.Label(
            footer,
            text="Proje türü: Data Visualization + AI Assistant + Price Comparison Simulation  |  Kısayol: F8",
            bg=THEME["bg"],
            fg=THEME["muted"],
            font=("Segoe UI", 9)
        )
        label.pack(side="left")

        close_btn = tk.Button(
            footer,
            text="Çıkış",
            command=self.root.destroy,
            bg=THEME["red"],
            fg="#111827",
            activebackground="#FDA4AF",
            activeforeground="#111827",
            relief="flat",
            font=("Segoe UI Semibold", 9),
            padx=14,
            pady=6,
            cursor="hand2"
        )
        close_btn.pack(side="right")

    def refresh_ollama_status(self):
        def worker():
            model = get_available_ollama_model()
            self.queue.put(("ollama_status", model))

        threading.Thread(target=worker, daemon=True).start()

    def set_ollama_status(self, model):
        if model:
            self.ollama_status_label.config(
                text=f"Ollama aktif • Model: {model}",
                fg=THEME["green"]
            )
        else:
            self.ollama_status_label.config(
                text="Ollama pasif • AI yorumu sınırlı",
                fg=THEME["yellow"]
            )

    def run_search(self):
        query = self.search_var.get().strip()
        if not query:
            messagebox.showwarning("Eksik Bilgi", "Lütfen bir yemek adı yaz.")
            return

        food_key, rows = build_synthetic_price_data(query)
        self.current_food = food_key
        self.current_rows = rows

        self.food_label.config(text=f"Aranan yemek: {food_key.title()}")
        self.populate_table(rows)
        self.populate_metrics(rows)
        self.draw_chart()
        self.set_ai_text(
            f"{food_key.title()} için fiyat karşılaştırması hazır.\n\n"
            f"En ucuz seçenek: {rows[0]['platform']} — {money(rows[0]['total'])}\n"
            f"AI yorum almak için 'AI Analiz' butonuna bas."
        )

    def populate_table(self, rows):
        for item in self.tree.get_children():
            self.tree.delete(item)

        for row in rows:
            values = (
                row["rank"],
                row["platform"],
                money(row["product_price"]),
                money(row["delivery_fee"]),
                money(row["service_fee"]),
                f"-{money(row['discount'])}" if row["discount"] else "0 TL",
                money(row["total"]),
                f"{row['rating']}/5",
                f"{row['delivery_min']} dk",
            )
            tag = "best" if row["rank"] == 1 else "normal"
            self.tree.insert("", "end", values=values, tags=(tag,))

        self.tree.tag_configure("best", background="#123524", foreground=THEME["text"])
        self.tree.tag_configure("normal", background=THEME["panel"], foreground=THEME["text"])

    def populate_metrics(self, rows):
        for child in self.metric_frame.winfo_children():
            child.destroy()

        metrics = calculate_project_metrics(rows)
        items = [
            ("En Ucuz", money(metrics["min_price"]), THEME["green"]),
            ("Ortalama", money(metrics["avg_price"]), THEME["blue"]),
            ("En Pahalı", money(metrics["max_price"]), THEME["red"]),
            ("Fiyat Farkı", f"%{metrics['spread_percent']:.1f}", THEME["yellow"]),
        ]

        for title, value, color in items:
            card = tk.Frame(self.metric_frame, bg=THEME["card"])
            card.pack(side="left", fill="x", expand=True, padx=(0, 10))

            tk.Label(
                card,
                text=title,
                bg=THEME["card"],
                fg=THEME["muted"],
                font=("Segoe UI", 9)
            ).pack(anchor="w", padx=12, pady=(10, 0))

            tk.Label(
                card,
                text=value,
                bg=THEME["card"],
                fg=color,
                font=("Segoe UI Black", 16)
            ).pack(anchor="w", padx=12, pady=(0, 10))

    def draw_chart(self):
        self.chart.delete("all")
        rows = self.current_rows
        if not rows:
            return

        width = max(self.chart.winfo_width(), 400)
        height = max(self.chart.winfo_height(), 280)

        pad_x = 56
        pad_y = 38
        chart_w = width - pad_x * 2
        chart_h = height - pad_y * 2

        max_total = max(r["total"] for r in rows)
        min_total = min(r["total"] for r in rows)
        upper = max_total * 1.18

        self.chart.create_text(
            pad_x,
            18,
            text="Toplam sepet maliyeti",
            fill=THEME["muted"],
            font=("Segoe UI Semibold", 10),
            anchor="w"
        )

        # Grid çizgileri
        for i in range(5):
            y = pad_y + chart_h - (chart_h / 4) * i
            value = upper / 4 * i
            self.chart.create_line(pad_x, y, width - pad_x + 8, y, fill="#263244")
            self.chart.create_text(
                pad_x - 8,
                y,
                text=f"{int(value)}",
                fill=THEME["muted"],
                font=("Segoe UI", 8),
                anchor="e"
            )

        bar_gap = 34
        bar_w = (chart_w - bar_gap * (len(rows) - 1)) / len(rows)
        colors = [THEME["green"], THEME["blue"], THEME["purple"]]

        for idx, row in enumerate(rows):
            x1 = pad_x + idx * (bar_w + bar_gap)
            x2 = x1 + bar_w

            bar_h = (row["total"] / upper) * chart_h
            y1 = pad_y + chart_h - bar_h
            y2 = pad_y + chart_h

            color = colors[idx % len(colors)]
            self.round_rectangle(x1, y1, x2, y2, radius=14, fill=color, outline="")

            self.chart.create_text(
                (x1 + x2) / 2,
                y1 - 16,
                text=money(row["total"]),
                fill=THEME["text"],
                font=("Segoe UI Semibold", 10)
            )

            self.chart.create_text(
                (x1 + x2) / 2,
                y2 + 18,
                text=row["platform"],
                fill=THEME["text"],
                font=("Segoe UI Semibold", 9)
            )

            diff_text = "En ucuz" if row["difference"] == 0 else f"+{money(row['difference'])}"
            self.chart.create_text(
                (x1 + x2) / 2,
                y2 + 38,
                text=diff_text,
                fill=THEME["muted"],
                font=("Segoe UI", 8)
            )

        self.chart.create_line(
            pad_x,
            pad_y + chart_h,
            width - pad_x + 8,
            pad_y + chart_h,
            fill=THEME["line"],
            width=2
        )

        # Basit trend çizgisi
        points = []
        for idx, row in enumerate(rows):
            x = pad_x + idx * (bar_w + bar_gap) + bar_w / 2
            y = pad_y + chart_h - (row["total"] / upper) * chart_h
            points.extend([x, y])

        if len(points) >= 4:
            self.chart.create_line(*points, fill=THEME["yellow"], width=3, smooth=True)

        self.chart.create_text(
            width - pad_x,
            18,
            text=f"Min: {money(min_total)} • Max: {money(max_total)}",
            fill=THEME["muted"],
            font=("Segoe UI", 9),
            anchor="e"
        )

    def round_rectangle(self, x1, y1, x2, y2, radius=20, **kwargs):
        points = [
            x1 + radius, y1,
            x2 - radius, y1,
            x2, y1,
            x2, y1 + radius,
            x2, y2 - radius,
            x2, y2,
            x2 - radius, y2,
            x1 + radius, y2,
            x1, y2,
            x1, y2 - radius,
            x1, y1 + radius,
            x1, y1,
        ]
        return self.chart.create_polygon(points, smooth=True, **kwargs)

    def run_ai_analysis(self):
        if not self.current_rows:
            self.run_search()

        rows = list(self.current_rows)
        food = self.current_food or self.search_var.get().strip()

        self.set_ai_text("Ollama AI analiz hazırlanıyor...\n\nLütfen birkaç saniye bekleyin.")

        def worker():
            result = ask_ollama(food, rows)
            self.queue.put(("ai_result", result))

        threading.Thread(target=worker, daemon=True).start()

    def set_ai_text(self, text):
        self.ai_text.config(state="normal")
        self.ai_text.delete("1.0", "end")
        self.ai_text.insert("1.0", text)
        self.ai_text.config(state="normal")

    def copy_ai_text(self):
        text = self.ai_text.get("1.0", "end").strip()
        if not text:
            return

        if pyperclip:
            pyperclip.copy(text)
            messagebox.showinfo("Kopyalandı", "AI yorumu panoya kopyalandı.")
        else:
            messagebox.showwarning("Eksik Paket", "pyperclip paketi bulunamadı.")

    def process_queue(self):
        try:
            while True:
                event, payload = self.queue.get_nowait()

                if event == "ai_result":
                    self.set_ai_text(payload)
                elif event == "ollama_status":
                    self.set_ollama_status(payload)
                elif event == "hotkey_search":
                    self.handle_hotkey_search(payload)

        except queue.Empty:
            pass

        self.root.after(120, self.process_queue)

    def handle_hotkey_search(self, selected_text):
        if not selected_text:
            messagebox.showwarning(
                "Seçim Bulunamadı",
                "Lütfen önce yemek adını seç, sonra F8'e bas."
            )
            return

        cleaned = selected_text.strip()
        if len(cleaned) > 60:
            cleaned = cleaned[:60]

        self.search_var.set(cleaned)
        self.root.deiconify()
        self.root.lift()
        self.root.focus_force()
        self.run_search()

    def start_hotkey_listener(self):
        if keyboard is None or HOTKEY is None:
            return

        def on_press(key):
            try:
                if key == HOTKEY and not self.hotkey_locked:
                    self.hotkey_locked = True

                    def worker():
                        selected = copy_selected_text()
                        self.queue.put(("hotkey_search", selected))

                    threading.Thread(target=worker, daemon=True).start()
            except Exception:
                pass

        def on_release(key):
            try:
                if key == HOTKEY:
                    self.hotkey_locked = False
            except Exception:
                pass

        try:
            self.listener = keyboard.Listener(on_press=on_press, on_release=on_release)
            self.listener.daemon = True
            self.listener.start()
        except Exception:
            pass


# ============================================================
# SPLASH / BAŞLATMA
# ============================================================

def show_startup_error_if_needed():
    missing = []

    if requests is None:
        missing.append("requests")
    if pyperclip is None:
        missing.append("pyperclip")
    if pyautogui is None:
        missing.append("pyautogui")
    if keyboard is None:
        missing.append("pynput")

    if missing:
        messagebox.showwarning(
            "Eksik Paket Uyarısı",
            "Bazı paketler eksik görünüyor:\n\n"
            + ", ".join(missing)
            + "\n\nrequirements.txt kurulumu yapılmadıysa kurulum.bat dosyasını çalıştır."
        )


def main():
    root = tk.Tk()
    show_startup_error_if_needed()
    FoodRadarApp(root)
    root.mainloop()


if __name__ == "__main__":
    main()
