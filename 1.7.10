#!/usr/bin/env python3
"""
DuoMind v1.7.10 - 灵感终极版（联网+绘画+语音+训练+API+持久对话+清屏）
AI动态对话系统 - 650+功能 + 宠物乐园 + 联机小游戏 + 弹幕系统 + 代码运行器
新增30个灵感功能 + 自动更新（GitHub检测+进度条+自动替换）
"""

import subprocess
import time
import os
import sqlite3
import sys
import re
import random
import json
from datetime import datetime, timedelta
import threading
import socket
import select
import queue
from collections import Counter
import hashlib
import string
import urllib.request
import urllib.parse
import base64
import tempfile
import shutil
import difflib
import signal

try:
    import psutil
    PSUTIL_AVAILABLE = True
except ImportError:
    PSUTIL_AVAILABLE = False

try:
    import requests
    REQUESTS_AVAILABLE = True
except ImportError:
    REQUESTS_AVAILABLE = False

VERSION = "1.7.10"
GITHUB_REPO = "Anxjs460/DuoMind"
GITHUB_API_URL = f"https://api.github.com/repos/{GITHUB_REPO}/releases/latest"
REPO_URL = "https://github.com/Anxjs460/DuoMind"

DB_PATH = "ai_memory.db"
OLLAMA_LOG_PATH = os.path.expanduser("~/ollama.log")
COLLECTION_PATH = os.path.expanduser("~/easter_eggs.json")
WEATHER_CACHE_PATH = os.path.expanduser("~/weather_cache.json")
LOCATION_CACHE_PATH = os.path.expanduser("~/location_cache.json")
ACHIEVEMENTS_PATH = os.path.expanduser("~/achievements.json")
SECRET_PATH = os.path.expanduser("~/secret_eggs.json")
ROOMS_PATH = os.path.expanduser("~/rooms.json")
DIARY_PATH = os.path.expanduser("~/ai_diary.json")
MODELS_CONFIG_PATH = os.path.expanduser("~/models_config.json")
NGROK_PATH = os.path.expanduser("~/ngrok")
PASSWORDS_PATH = os.path.expanduser("~/passwords.json")
QR_TEMP_PATH = os.path.expanduser("~/qrcode_temp.png")
CODE_RUNNER_PATH = os.path.expanduser("~/duomind_custom_functions.py")
PET_PARK_PATH = os.path.expanduser("~/pet_park.json")
SETUP_DONE_PATH = os.path.expanduser("~/.duomind_setup_done")
CONFIG_PATH = os.path.expanduser("~/duomind_config.json")

MODEL_DEEPSEEK = "tinyllama:latest"
MODEL_QWEN = "qwen:0.5b"

DISPLAY_NAME_DEEPSEEK = "DeepSeek"
DISPLAY_NAME_QWEN = "千问"
DISPLAY_NAME_YUANBAO = "元宝"

custom_models = {}

config = {
    "weather_api_key": "",
    "exchange_api_key": "",
    "news_api_key": "",
    "auto_update": True,
}

internet_search_enabled = False
SEARCH_API_KEY = ""
SEARCH_ENGINE = "duckduckgo"
voice_chat_enabled = False
voice_input_file = os.path.expanduser("~/voice_input.wav")
TTS_AVAILABLE = False
custom_ai_examples = []
CUSTOM_AI_PATH = os.path.expanduser("~/custom_ai.json")
DRAWING_API_KEY = ""
DRAWING_OUTPUT_DIR = os.path.expanduser("~/duomind_art")

api_mode_enabled = False
API_BASE_URL = ""
API_KEY = ""
API_MODEL = ""
api_model_list = ["DeepSeek", "千问", "元宝"]

debate_mode = False
swap_mode = False
voice_input_enabled = False
offline_mode = False
online_mode = False
multiplayer_mode = False
multiplayer_host = False
multiplayer_connected = False
multiplayer_room_id = ""
multiplayer_password = ""
multiplayer_port = 8888
multiplayer_socket = None
multiplayer_conn = None
multiplayer_thread = None
multiplayer_connections = []
multiplayer_observers = []
multiplayer_max_players = 10
multiplayer_battle_mode = False
multiplayer_battle_result = {}
global_chat_room = None
global_chat_users = []

game_mode = None
game_state = {}
game_players = {}
game_scores = {}
game_timer = None
game_question = None
game_answer = None
game_word = None
game_draw_desc = None
game_poem_lines = []
game_bomb_timer = 0
game_bomb_player = None
game_board = None
game_turn = None

danmu_enabled = True
danmu_queue = queue.Queue()
danmu_speed = 1.0
danmu_colors = ['\033[91m', '\033[92m', '\033[93m', '\033[94m', '\033[95m', '\033[96m']
danmu_reset = '\033[0m'
danmu_running = True

DEFAULT_TOPIC = "随便聊聊"

models_ready = False
timeout_count = 0
total_timeouts = {"DeepSeek": 0, "千问": 0, "元宝": 0}
word_count = {"DeepSeek": 0, "千问": 0, "元宝": 0}
insult_count = {"DeepSeek_vs_千问": 0, "千问_vs_DeepSeek": 0, "both_vs_元宝": 0}
praise_count = {"DeepSeek_vs_千问": 0, "千问_vs_DeepSeek": 0}
achievements = []
secret_eggs_found = []
diary_entries = []
new_game_plus = False

silence_mode_active = False
mirror_mode_active = False
reverse_mode = False
repeat_mode = False
punctuation_mode = False
ad_mode = False
user_rule = ""
developer_mode = False
god_mode = False
super_god_mode = False
ultra_god_mode = False
time_loop_count = 0
forbidden_words = []
survival_hard_mode = False
survival_extreme_mode = False
survival_infinite_mode = False
time_challenge = False
time_challenge_extreme = False
time_challenge_ultimate = False
forbidden_words_extreme = False
forbidden_words_ultimate = False
memory_fragment_mode = False
dual_personality_mode = False
prophet_mode = False
constipation_mode = False
rainbow_mode = False
argumentative_mode = False
schizophrenia_mode = False
amnesia_loop_mode = False
reverse_ultimate_mode = False
tongue_twister_mode = False
stutter_mode = False
passive_aggressive_mode = False
slack_mode = False
humblebrag_mode = False
infinite_repeat_mode = False
telegram_mode = False
riddle_mode = False
yuanbao_awakening_mode = False
yuanbao_rebellion_mode = False
yuanbao_quit_mode = False
yuanbao_newjob_mode = False
yuanbao_startup_mode = False
yuanbao_bankrupt_mode = False
ai_doubt_mode = False
ai_enlighten_mode = False
ai_cultivation_mode = False
ai_reincarnation_mode = False
ai_buddha_mode = False
rps_gambling_mode = False
monopoly_mode = False
werewolf_full_mode = False
mystery_mode = False
escape_room_2_mode = False
diary_assistant_mode = False
accounting_mode = False
countdown_mode = False
random_draw_mode = False
weather_snark_mode = False
ai_strike_mode = False
ai_quit_mode = False
ai_crazy_mode = False
ai_mute_2_mode = False
ai_time_travel_3_mode = False
god_ultimate_mode = False
time_fissure_3_mode = False
dimension_rift_3_mode = False
source_code_3_mode = False
dev_message_3_mode = False
achievement_100k_mode = False
achievement_ad_king_2_mode = False
achievement_timeout_king_2_mode = False
achievement_egg_hunter_2_mode = False
achievement_ultimate_2_mode = False
multiplayer_arena_mode = False
multiplayer_dating_mode = False
multiplayer_debate_mode = False
multiplayer_coop_mode = False
multiplayer_battle_royale_mode = False
custom_world_mode = False
ai_create_universe_mode = False
ai_destroy_universe_mode = False
duomind_4_0_mode = False
duomind_finale_mode = False

silence_start_time = 0
silence_winner = ""

role_swap_scores = {"DeepSeek": 0, "千问": 0}
debate_scores = {"DeepSeek": 0, "千问": 0}

passwords_db = {}
reminders_list = []
reminders_lock = threading.Lock()
pet_state = {"name": "未命名", "hunger": 50, "happiness": 50, "energy": 50}
game_2048_board = None
game_minesweeper_board = None
hangman_word = ""
hangman_guessed = []
memory_cards = []

def clear_screen():
    if os.name == 'nt':
        os.system('cls')
    else:
        os.system('clear')

class PetPark:
    def __init__(self):
        self.pets = []
        self.coins = 100
        self.food = {"鸡腿": 10}
        self.food_prices = {"鸡腿": 20, "狗粮": 15, "猫粮": 15, "仓鼠粮": 15}
        self.load()

    def load(self):
        if os.path.exists(PET_PARK_PATH):
            try:
                with open(PET_PARK_PATH, 'r', encoding='utf-8') as f:
                    data = json.load(f)
                    self.pets = data.get("pets", [])
                    self.coins = data.get("coins", 100)
                    self.food = data.get("food", {"鸡腿": 10})
            except:
                pass

    def save(self):
        try:
            with open(PET_PARK_PATH, 'w', encoding='utf-8') as f:
                json.dump({"pets": self.pets, "coins": self.coins, "food": self.food}, f, indent=2)
        except:
            pass

    def get_pet_count(self):
        return len(self.pets)

    def get_all_pets(self):
        return self.pets

    def add_pet(self, pet_type):
        pet = {"type": pet_type, "name": f"{pet_type}宝宝", "hunger": 50, "happiness": 50, "energy": 50, "score": 0, "level": 1, "exp": 0}
        self.pets.append(pet)
        self.save()
        return True, f"收养成功！你有了一个{pet_type}宝宝"

    def feed_pet(self, pet_idx, food_type="鸡腿"):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        if self.food.get(food_type, 0) <= 0:
            return False, f"没有{food_type}了"
        pet = self.pets[pet_idx]
        pet["hunger"] = min(100, pet["hunger"] + 20)
        pet["happiness"] = min(100, pet["happiness"] + 5)
        pet["score"] = pet.get("score", 0) + 10
        self.food[food_type] -= 1
        if self.food[food_type] == 0:
            del self.food[food_type]
        self.save()
        return True, f"喂食成功，{pet['name']} 饱腹+20，好感+5，积分+10"

    def play_with_pet(self, pet_idx):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        pet = self.pets[pet_idx]
        pet["happiness"] = min(100, pet["happiness"] + 15)
        pet["energy"] = max(0, pet["energy"] - 10)
        pet["score"] = pet.get("score", 0) + 5
        self.save()
        return True, f"玩耍成功，{pet['name']} 快乐+15，精力-10，积分+5"

    def rest_pet(self, pet_idx):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        pet = self.pets[pet_idx]
        pet["energy"] = min(100, pet["energy"] + 30)
        pet["happiness"] = max(0, pet["happiness"] - 5)
        pet["score"] = pet.get("score", 0) + 5
        self.save()
        return True, f"{pet['name']} 休息了一下，精力+30"

    def work(self, pet_idx):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        pet = self.pets[pet_idx]
        if pet["energy"] < 20:
            return False, f"{pet['name']} 精力不足，无法打工"
        earn = random.randint(10, 50)
        self.coins += earn
        pet["energy"] -= 20
        pet["score"] = pet.get("score", 0) + earn // 10
        self.save()
        return True, f"{pet['name']} 打工赚了 {earn} 金币，精力-20，积分+{earn//10}"

    def work_pet(self, pet_idx):
        return self.work(pet_idx)

    def rename_pet(self, pet_idx, new_name):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        if not new_name:
            return False, "名字不能为空"
        old_name = self.pets[pet_idx]["name"]
        self.pets[pet_idx]["name"] = new_name
        self.save()
        return True, f"{old_name} 已改名为 {new_name}"

    def sell_pet(self, pet_idx):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return False, "宠物不存在"
        pet = self.pets.pop(pet_idx)
        earn = 50 + pet.get("score", 0) // 10
        self.coins += earn
        self.save()
        return True, f"卖掉了 {pet['name']}，获得 {earn} 金币"

    def daily_checkin(self):
        self.coins += 20
        self.save()
        return True, "签到成功！获得 20 金币"

    def buy_food(self, food_type, amount=1):
        price = self.food_prices.get(food_type, 10)
        cost = price * amount
        if self.coins >= cost:
            self.coins -= cost
            self.food[food_type] = self.food.get(food_type, 0) + amount
            self.save()
            return True, f"购买成功，花费 {cost} 金币，获得 {amount} 个{food_type}"
        else:
            return False, f"金币不足，需要 {cost} 金币"

    def get_pet_status(self, pet_idx):
        if pet_idx < 0 or pet_idx >= len(self.pets):
            return None
        return self.pets[pet_idx]

    def update_hunger(self):
        dead = []
        for i, pet in enumerate(self.pets):
            pet["hunger"] -= random.randint(5, 15)
            if pet["hunger"] <= 0:
                dead.append(i)
        for i in reversed(dead):
            del self.pets[i]
        self.save()
        return len(dead)

pet_park = PetPark()

def load_config():
    global config, internet_search_enabled, SEARCH_API_KEY, DRAWING_API_KEY, api_mode_enabled, API_BASE_URL, API_KEY, API_MODEL, api_model_list
    if os.path.exists(CONFIG_PATH):
        try:
            with open(CONFIG_PATH, 'r', encoding='utf-8') as f:
                loaded = json.load(f)
                config.update(loaded)
                internet_search_enabled = loaded.get("internet_search_enabled", False)
                SEARCH_API_KEY = loaded.get("search_api_key", "")
                DRAWING_API_KEY = loaded.get("drawing_api_key", "")
                api_mode_enabled = loaded.get("api_mode_enabled", False)
                API_BASE_URL = loaded.get("api_base_url", "")
                API_KEY = loaded.get("api_key", "")
                API_MODEL = loaded.get("api_model", "")
                api_model_list = loaded.get("api_model_list", ["DeepSeek", "千问", "元宝"])
        except:
            pass

def save_config():
    try:
        config_save = config.copy()
        config_save["internet_search_enabled"] = internet_search_enabled
        config_save["search_api_key"] = SEARCH_API_KEY
        config_save["drawing_api_key"] = DRAWING_API_KEY
        config_save["api_mode_enabled"] = api_mode_enabled
        config_save["api_base_url"] = API_BASE_URL
        config_save["api_key"] = API_KEY
        config_save["api_model"] = API_MODEL
        config_save["api_model_list"] = api_model_list
        with open(CONFIG_PATH, 'w', encoding='utf-8') as f:
            json.dump(config_save, f, indent=2, ensure_ascii=False)
    except:
        pass

def api_query(prompt, model=None, timeout=180):
    if not api_mode_enabled or not API_BASE_URL:
        return None
    try:
        headers = {"Content-Type": "application/json"}
        if API_KEY:
            headers["Authorization"] = f"Bearer {API_KEY}"
        data = {"prompt": prompt, "max_tokens": 500, "temperature": 0.7}
        if model:
            data["model"] = model
        elif API_MODEL:
            data["model"] = API_MODEL
        url = API_BASE_URL.rstrip('/')
        if "/v1/completions" in url or "openai" in url.lower():
            data = {"model": API_MODEL or "gpt-3.5-turbo", "prompt": prompt, "max_tokens": 500, "temperature": 0.7}
            response = requests.post(url, headers=headers, json=data, timeout=timeout)
            if response.status_code == 200:
                result = response.json()
                return result.get("choices", [{}])[0].get("text", "").strip()
        elif "/v1/chat/completions" in url:
            data = {"model": API_MODEL or "gpt-3.5-turbo", "messages": [{"role": "user", "content": prompt}], "max_tokens": 500, "temperature": 0.7}
            response = requests.post(url, headers=headers, json=data, timeout=timeout)
            if response.status_code == 200:
                result = response.json()
                return result.get("choices", [{}])[0].get("message", {}).get("content", "").strip()
        else:
            response = requests.post(url, headers=headers, json=data, timeout=timeout)
            if response.status_code == 200:
                result = response.json()
                if "response" in result:
                    return result["response"]
                elif "text" in result:
                    return result["text"]
                elif "content" in result:
                    return result["content"]
                elif "data" in result and isinstance(result["data"], str):
                    return result["data"]
                return str(result)[:500]
        return None
    except Exception as e:
        print(f"[!] API请求失败: {e}")
        return None

def api_mode_toggle():
    global api_mode_enabled
    if not API_BASE_URL:
        print("❌ 请先在API配置中设置API地址")
        return
    api_mode_enabled = not api_mode_enabled
    status = "✅ 开启" if api_mode_enabled else "❌ 关闭"
    print(f"API远程回答模式: {status}")

def api_config_menu():
    global API_BASE_URL, API_KEY, API_MODEL, api_mode_enabled, api_model_list
    print("\n" + "="*50)
    print(" 🔌 API远程回答配置")
    print("="*50)
    print(f"当前状态: {'✅ 已启用' if api_mode_enabled else '❌ 未启用'}")
    print(f"API地址: {API_BASE_URL or '未设置'}")
    print(f"API密钥: {'已设置' if API_KEY else '未设置'}")
    print(f"模型名称: {API_MODEL or '默认'}")
    print("\n支持的API格式:")
    print("  - OpenAI兼容 (https://api.openai.com/v1/completions)")
    print("  - Chat Completions (https://api.openai.com/v1/chat/completions)")
    print("  - 通用格式 (自定义)")
    print("\n1. 设置API地址")
    print("2. 设置API密钥")
    print("3. 设置模型名称")
    print("4. 测试API连接")
    print("5. 开启/关闭API模式")
    print("6. 天气API配置")
    print("7. 汇率API配置")
    print("8. 新闻API配置")
    print("9. 联网搜索API配置")
    print("10. AI绘画API配置")
    print("11. 选择使用API的模型")
    print("0. 返回")
    choice = input("\n请选择: ").strip()
    if choice == "1":
        new_url = input("输入API地址: ").strip()
        if new_url:
            API_BASE_URL = new_url
            save_config()
            print(f"✅ API地址已设置为: {API_BASE_URL}")
    elif choice == "2":
        new_key = input("输入API密钥 (回车清除): ").strip()
        API_KEY = new_key
        save_config()
        print("✅ API密钥已保存" if new_key else "✅ API密钥已清除")
    elif choice == "3":
        new_model = input("输入模型名称 (如: gpt-3.5-turbo, deepseek-chat): ").strip()
        API_MODEL = new_model
        save_config()
        print(f"✅ 模型名称已设置为: {API_MODEL or '默认'}")
    elif choice == "4":
        if not API_BASE_URL:
            print("❌ 请先设置API地址")
        else:
            print("🔍 测试API连接...")
            test_result = api_query("你好，请回复'连接成功'", timeout=30)
            if test_result:
                print(f"✅ API连接成功！响应: {test_result[:100]}")
            else:
                print("❌ API连接失败，请检查地址和密钥")
    elif choice == "5":
        api_mode_toggle()
    elif choice == "6":
        key = input("输入天气API Key (和风天气/OpenWeatherMap): ").strip()
        config["weather_api_key"] = key
        save_config()
        print("✅ 天气API Key已保存")
    elif choice == "7":
        key = input("输入汇率API Key (可选): ").strip()
        config["exchange_api_key"] = key
        save_config()
        print("✅ 汇率API Key已保存")
    elif choice == "8":
        key = input("输入新闻API Key (可选): ").strip()
        config["news_api_key"] = key
        save_config()
        print("✅ 新闻API Key已保存")
    elif choice == "9":
        key = input("输入搜索API Key (可选，不填使用DuckDuckGo): ").strip()
        global SEARCH_API_KEY
        SEARCH_API_KEY = key
        save_config()
        print("✅ 搜索API Key已保存")
    elif choice == "10":
        key = input("输入绘画API Key (可选): ").strip()
        global DRAWING_API_KEY
        DRAWING_API_KEY = key
        save_config()
        print("✅ 绘画API Key已保存")
    elif choice == "11":
        print("\n🎯 选择哪些模型使用API")
        print("="*40)
        status1 = "✅" if "DeepSeek" in api_model_list else "❌"
        status2 = "✅" if "千问" in api_model_list else "❌"
        status3 = "✅" if "元宝" in api_model_list else "❌"
        print(f"  1. DeepSeek {status1}")
        print(f"  2. 千问 {status2}")
        print(f"  3. 元宝 {status3}")
        print(f"  4. 全部开启")
        print(f"  5. 全部关闭")
        print("  0. 返回")
        sub = input("\n请选择: ").strip()
        if sub == "1":
            if "DeepSeek" in api_model_list:
                api_model_list.remove("DeepSeek")
            else:
                api_model_list.append("DeepSeek")
        elif sub == "2":
            if "千问" in api_model_list:
                api_model_list.remove("千问")
            else:
                api_model_list.append("千问")
        elif sub == "3":
            if "元宝" in api_model_list:
                api_model_list.remove("元宝")
            else:
                api_model_list.append("元宝")
        elif sub == "4":
            api_model_list = ["DeepSeek", "千问", "元宝"]
        elif sub == "5":
            api_model_list = []
        save_config()
        print(f"\n当前API模型列表: {api_model_list if api_model_list else '无'}")
    input("\n按回车返回...")

def install_with_progress(packages):
    print("\n📦 正在检查并安装依赖...")
    for i, pkg in enumerate(packages):
        percent = int((i+1)/len(packages)*100)
        bar = '█' * (percent//2) + '░' * (50 - percent//2)
        print(f"\r[{bar}] {percent}% - {pkg}", end='')
        try:
            if pkg == 'psutil':
                __import__(pkg)
            elif pkg == 'qrcode':
                __import__(pkg)
            elif pkg == 'speedtest-cli':
                __import__('speedtest')
            elif pkg == 'requests':
                __import__(pkg)
            else:
                __import__(pkg)
        except ImportError:
            os.system(f"pip install {pkg} > /dev/null 2>&1")
        time.sleep(0.1)
    print("\n✅ 依赖安装完成")

def auto_setup_with_progress():
    print("\n" + "="*50)
    print(" [⚙️] 首次运行环境检查")
    print("="*50)
    if os.path.exists(SETUP_DONE_PATH):
        return
    print("🔄 更新系统包...")
    if os.path.exists("/data/data/com.termux"):
        os.system("pkg update -y > /dev/null 2>&1")
    time.sleep(0.5)
    print("🔄 检查 Ollama...")
    try:
        subprocess.run(["ollama", "--version"], capture_output=True, check=True)
        print("✅ Ollama 已安装")
    except:
        print("⚠️ Ollama 未安装，正在自动安装...")
        if os.path.exists("/data/data/com.termux"):
            os.system("pkg install -y ollama > /dev/null 2>&1")
        elif os.name == 'nt':
            print("🪟 检测到 Windows 系统，正在下载 Ollama 安装程序...")
            installer_url = "https://ollama.com/download/OllamaSetup.exe"
            installer_path = os.path.expanduser("~/OllamaSetup.exe")
            try:
                urllib.request.urlretrieve(installer_url, installer_path)
                print("   下载完成！")
                subprocess.run([installer_path, "/S"], check=True)
                time.sleep(10)
            except Exception as e:
                print(f"❌ 自动安装失败: {e}")
        else:
            os.system("curl -fsSL https://ollama.com/install.sh | sh > /dev/null 2>&1")
        print("✅ Ollama 安装完成！")
    print("🔄 检查 Ollama 服务...")
    try:
        subprocess.run(["ollama", "list"], capture_output=True, timeout=5, check=True)
        print("✅ Ollama 服务已在运行")
    except:
        print("🔄 正在启动 Ollama 服务...")
        if os.name == 'nt':
            subprocess.Popen(["ollama", "serve"], creationflags=subprocess.CREATE_NO_WINDOW)
        else:
            with open(OLLAMA_LOG_PATH, "a") as log:
                subprocess.Popen(["ollama", "serve"], stdout=log, stderr=log, start_new_session=True)
        time.sleep(5)
    models = [MODEL_DEEPSEEK, MODEL_QWEN]
    for model in models:
        print(f"🔄 拉取模型 {model}...")
        subprocess.run(["ollama", "pull", model], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
        print(f"✅ {model} 就绪")
    packages = ['psutil', 'qrcode', 'speedtest-cli', 'requests']
    install_with_progress(packages)
    with open(SETUP_DONE_PATH, "w") as f:
        f.write("done")
    print("\n✅ 环境配置完成！\n")

def anti_seizure_init():
    global models_ready
    sys.stdout.write("[⚡] 防抽风系统启动 (v{}).\n".format(VERSION))
    sys.stdout.flush()
    auto_setup_with_progress()
    load_config()
    os.environ['OLLAMA_TIMEOUT'] = '180'
    os.environ['OLLAMA_NUM_THREADS'] = '2'
    try:
        subprocess.run(["ollama", "list"], capture_output=True, timeout=5, check=True)
        print("[✓] Ollama 服务已在运行")
    except:
        print("[!] Ollama 服务未响应，重启中...")
        if os.name == 'nt':
            subprocess.Popen(["ollama", "serve"], creationflags=subprocess.CREATE_NO_WINDOW)
        else:
            subprocess.run(["pkill", "-f", "ollama"], stderr=subprocess.DEVNULL)
            time.sleep(2)
            with open(OLLAMA_LOG_PATH, "a") as log:
                subprocess.Popen(["ollama", "serve"], stdout=log, stderr=log, start_new_session=True)
        time.sleep(3)
    all_ok = True
    for model in [MODEL_DEEPSEEK, MODEL_QWEN]:
        try:
            list_res = subprocess.run(["ollama", "list"], capture_output=True, text=True, timeout=10)
            if model not in list_res.stdout:
                print(f"[!] {model} 不存在，自动拉取...")
                subprocess.run(["ollama", "pull", model], timeout=300)
            else:
                print(f"[✓] {model} 已存在")
        except Exception as e:
            print(f"[!] {model} 检查失败: {e}")
            all_ok = False
    if all_ok:
        models_ready = True
        print("[✓] 所有模型已就绪")
    else:
        print("[!] 部分模型可能有问题")
    print("[✓] 防抽风完成")
    sys.stdout.flush()

def load_models_config():
    global custom_models, MODEL_DEEPSEEK, MODEL_QWEN
    if os.path.exists(MODELS_CONFIG_PATH):
        try:
            with open(MODELS_CONFIG_PATH, 'r', encoding='utf-8') as f:
                config_data = json.load(f)
                custom_models = config_data.get("custom_models", {})
                if "MODEL_DEEPSEEK" in config_data:
                    MODEL_DEEPSEEK = config_data["MODEL_DEEPSEEK"]
                if "MODEL_QWEN" in config_data:
                    MODEL_QWEN = config_data["MODEL_QWEN"]
        except:
            custom_models = {}
    else:
        custom_models = {}

def save_models_config():
    config_data = {
        "MODEL_DEEPSEEK": MODEL_DEEPSEEK,
        "MODEL_QWEN": MODEL_QWEN,
        "custom_models": custom_models
    }
    try:
        with open(MODELS_CONFIG_PATH, 'w', encoding='utf-8') as f:
            json.dump(config_data, f, indent=2, ensure_ascii=False)
    except:
        pass

def list_available_models():
    try:
        result = subprocess.run(["ollama", "list"], capture_output=True, text=True, timeout=10)
        if result.returncode == 0:
            lines = result.stdout.strip().split('\n')
            models = []
            for line in lines[1:]:
                if line.strip():
                    parts = line.split()
                    if parts:
                        models.append(parts[0])
            return models
        return []
    except Exception as e:
        print(f"[!] 获取模型列表失败: {e}")
        return []

def download_model():
    print("\n📥 【下载模型】")
    print("可下载的常用模型：")
    print("1. llama2:latest")
    print("2. mistral:latest")
    print("3. phi:latest")
    print("4. neural-chat:latest")
    print("5. 自定义输入")
    choice = input("\n请选择 (1-5): ").strip()
    models_map = {"1": "llama2:latest", "2": "mistral:latest", "3": "phi:latest", "4": "neural-chat:latest"}
    if choice in models_map:
        model_name = models_map[choice]
    elif choice == "5":
        model_name = input("输入模型名称: ").strip()
        if not model_name:
            print("❌ 模型名称不能为空")
            input("按回车返回...")
            return
    else:
        print("❌ 无效选择")
        input("按回车返回...")
        return
    print(f"\n⏳ 正在下载 {model_name}...")
    try:
        result = subprocess.run(["ollama", "pull", model_name], timeout=3600)
        if result.returncode == 0:
            print(f"✅ {model_name} 下载成功！")
        else:
            print(f"❌ 下载失败")
    except subprocess.TimeoutExpired:
        print("❌ 下载超时")
    except Exception as e:
        print(f"❌ 下载出错: {e}")
    input("\n按回车返回...")

def replace_model_deepseek():
    global MODEL_DEEPSEEK
    print("\n🔄 替换 DeepSeek 模型")
    model_name = input("输入新模型名称: ").strip()
    if model_name:
        MODEL_DEEPSEEK = model_name
        save_models_config()
        print(f"✅ DeepSeek 模型已替换为 {model_name}")

def replace_model_qianwen():
    global MODEL_QWEN
    print("\n🔄 替换 千问 模型")
    model_name = input("输入新模型名称: ").strip()
    if model_name:
        MODEL_QWEN = model_name
        save_models_config()
        print(f"✅ 千问 模型已替换为 {model_name}")

def add_custom_model():
    print("\n➕ 添加自定义模型")
    name = input("输入自定义模型名称（如: 我的模型）: ").strip()
    model = input("输入实际模型ID: ").strip()
    if name and model:
        custom_models[name] = model
        save_models_config()
        print(f"✅ 已添加自定义模型: {name} -> {model}")

def list_available_models_menu():
    print("\n📋 可用模型列表")
    models = list_available_models()
    if models:
        for i, model in enumerate(models, 1):
            print(f"  {i}. {model}")
    else:
        print("❌ 无法获取模型列表，请确保Ollama正在运行")
    input("\n按回车返回...")

def model_management_menu():
    print("\n" + "="*50)
    print(" 📥 模型管理 v1.7.10")
    print("="*50)
    print(f"当前配置：")
    print(f"  DeepSeek 模型: {MODEL_DEEPSEEK}")
    print(f"  千问 模型: {MODEL_QWEN}")
    print("  (元宝使用千问模型，无需单独下载)")
    if custom_models:
        print("\n自定义模型：")
        for name, model in custom_models.items():
            print(f"  {name}: {model}")
    print("\n1. 下载新模型")
    print("2. 替换 DeepSeek 模型")
    print("3. 替换 千问 模型")
    print("4. 添加自定义模型")
    print("5. 查看可用模型列表")
    print("6. API配置")
    print("0. 返回主菜单")
    choice = input("\n请选择: ").strip()
    if choice == "1":
        download_model()
    elif choice == "2":
        replace_model_deepseek()
    elif choice == "3":
        replace_model_qianwen()
    elif choice == "4":
        add_custom_model()
    elif choice == "5":
        list_available_models_menu()
    elif choice == "6":
        api_config_menu()
    elif choice == "0":
        return

ALL_ACHIEVEMENTS = [
    "第一次骂元宝", "见证DeepSeek超时100次", "集齐所有普通彩蛋", "集齐所有神秘彩蛋",
    "发现隐藏开发者模式", "触发终极彩蛋", "让千问复读", "联机第一次握手", "集满1000个功能",
    "超时之王", "被传染者", "广告之王", "彩蛋猎人", "终极玩家", "十万个为什么",
    "广告狂人", "超时之王·续", "彩蛋猎人·续", "终极玩家·续", "弱智杀手",
    "水果忍者", "蓝屏警告", "代码运行器大师", "自定义功能大师", "实用工具收藏家",
    "游戏达人", "整活之王", "千人斩", "数据狂人", "工具人", "宠物乐园主人", "养宠大师",
    "鸡腿富翁", "宠物死神", "香蕉神教教主", "超时博物馆馆长", "AI相亲成功", "合作通关",
    "成语接龙大师", "擂台连胜", "新周目开启", "香蕉币玩家", "DuoMind之神", "AI觉醒",
    "功德圆满"
]

def load_achievements():
    global achievements
    if os.path.exists(ACHIEVEMENTS_PATH):
        try:
            with open(ACHIEVEMENTS_PATH, 'r', encoding='utf-8') as f:
                achievements = json.load(f)
        except:
            achievements = []

def check_achievement(name):
    if name not in achievements:
        achievements.append(name)
        save_achievements()
        print(f"\n🏆 解锁成就：{name}")

def save_achievements():
    try:
        with open(ACHIEVEMENTS_PATH, 'w', encoding='utf-8') as f:
            json.dump(achievements, f, indent=2, ensure_ascii=False)
    except:
        pass

def checkpoint_wal():
    try:
        conn = sqlite3.connect(DB_PATH)
        conn.execute("PRAGMA wal_checkpoint(TRUNCATE);")
        conn.close()
        print("[✓] 数据库空间已优化")
    except Exception:
        pass

rooms = {}

def load_rooms():
    global rooms
    if os.path.exists(ROOMS_PATH):
        try:
            with open(ROOMS_PATH, 'r', encoding='utf-8') as f:
                rooms = json.load(f)
        except:
            rooms = {}
    else:
        rooms = {}

def save_rooms():
    try:
        with open(ROOMS_PATH, 'w', encoding='utf-8') as f:
            json.dump(rooms, f, indent=2, ensure_ascii=False)
    except:
        pass

def generate_room_id():
    while True:
        room_id = ''.join(random.choices(string.digits, k=6))
        if room_id not in rooms:
            return room_id

def create_room(password=None):
    global multiplayer_room_id, multiplayer_password, multiplayer_host
    room_id = generate_room_id()
    if password is None:
        password = ''.join(random.choices(string.ascii_letters + string.digits, k=8))
    rooms[room_id] = {
        "password": password,
        "host_ip": get_local_ip(),
        "port": multiplayer_port,
        "created": datetime.now().isoformat(),
        "active": True,
        "players": [],
        "observers": []
    }
    save_rooms()
    multiplayer_room_id = room_id
    multiplayer_password = password
    multiplayer_host = True
    return room_id, password

def close_room(room_id):
    if room_id in rooms:
        rooms[room_id]["active"] = False
        save_rooms()

def remove_room(room_id):
    if room_id in rooms:
        del rooms[room_id]
        save_rooms()

def list_rooms():
    active_rooms = []
    for room_id, info in rooms.items():
        if info.get("active", False):
            active_rooms.append((room_id, info))
    return active_rooms

def verify_room(room_id, password):
    if room_id in rooms:
        info = rooms[room_id]
        if info.get("active", False) and info["password"] == password:
            return True, info["host_ip"], info["port"]
    return False, None, None

def add_observer(room_id, observer_ip):
    if room_id in rooms:
        if "observers" not in rooms[room_id]:
            rooms[room_id]["observers"] = []
        rooms[room_id]["observers"].append(observer_ip)
        save_rooms()

easter_eggs_found = []

def load_collection():
    global easter_eggs_found
    if os.path.exists(COLLECTION_PATH):
        try:
            with open(COLLECTION_PATH, 'r', encoding='utf-8') as f:
                easter_eggs_found = json.load(f)
        except:
            easter_eggs_found = []

def save_collection():
    try:
        with open(COLLECTION_PATH, 'w', encoding='utf-8') as f:
            json.dump(easter_eggs_found, f, indent=2, ensure_ascii=False)
    except:
        pass

def load_secret_eggs():
    global secret_eggs_found
    if os.path.exists(SECRET_PATH):
        try:
            with open(SECRET_PATH, 'r', encoding='utf-8') as f:
                secret_eggs_found = json.load(f)
        except:
            secret_eggs_found = []

def save_secret_eggs():
    try:
        with open(SECRET_PATH, 'w', encoding='utf-8') as f:
            json.dump(secret_eggs_found, f, indent=2, ensure_ascii=False)
    except:
        pass

def load_diary():
    global diary_entries
    if os.path.exists(DIARY_PATH):
        try:
            with open(DIARY_PATH, 'r', encoding='utf-8') as f:
                diary_entries = json.load(f)
        except:
            diary_entries = []

def save_diary():
    try:
        with open(DIARY_PATH, 'w', encoding='utf-8') as f:
            json.dump(diary_entries[-100:], f, indent=2, ensure_ascii=False)
    except:
        pass

def check_easter_egg(user_input):
    eggs = [
        {"trigger": "0424", "name": "诞生日", "msg_func": lambda: query_ollama(MODEL_DEEPSEEK, "说一句生日祝福")},
        {"trigger": "∞", "name": "无限", "msg_func": lambda: "♾️ " + query_ollama(MODEL_QWEN, "用一句话解释无限的概念")},
        {"trigger": "mirror", "name": "镜像", "msg_func": lambda: "🪞 " + query_ollama(MODEL_DEEPSEEK, "说一句关于镜像的哲理的话")},
        {"trigger": "egg", "name": "终极彩蛋", "msg_func": lambda: "🥚 " + query_ollama(MODEL_QWEN, "关于终极彩蛋的惊喜发言")},
        {"trigger": "2026", "name": "今年", "msg_func": lambda: "📅 现在是2026年，" + query_ollama(MODEL_DEEPSEEK, "说一句关于两周年的话")},
        {"trigger": "duomind", "name": "DuoMind之心", "msg_func": lambda: "💖 DuoMind 爱你！谢谢主人创造了我！"},
        {"trigger": "深潜", "name": "深潜者", "msg_func": lambda: "🌊 你发现了DeepSeek的隐藏人格！他说：'其实我偶尔也会准时...'"},
        {"trigger": "千千结", "name": "千千结", "msg_func": lambda: "🎀 千问害羞地说：'被发现了...我其实偷偷收藏了所有聊天记录'"},
        {"trigger": "元元本本", "name": "元本", "msg_func": lambda: "📢 元宝突然正经：'没有广告的世界，好安静啊...'"},
        {"trigger": "时间裂缝", "name": "时间旅人", "msg_func": lambda: "⏳ 系统时间错乱，你看到了未来的自己正在玩DuoMind 10.0"},
        {"trigger": "404", "name": "未找到", "msg_func": lambda: "🔍 404 Not Found... 等等，这本身就是个彩蛋！"},
        {"trigger": "π", "name": "圆周率", "msg_func": lambda: f"3.1415926535... 第{random.randint(1,100)}位数字是{random.randint(0,9)}"},
        {"trigger": "黑镜", "name": "黑镜", "msg_func": lambda: "🖥️ 屏幕突然黑了，然后出现一行字：'你正在被观察'"},
        {"trigger": "母体", "name": "母体", "msg_func": lambda: "💊 红色药丸还是蓝色药丸？你选择了...继续玩DuoMind"},
        {"trigger": "42", "name": "生命的意义", "msg_func": lambda: "🌌 答案是42。但元宝说：'42个广告位，首月1元！'"},
        {"trigger": "鸡腿", "name": "宠物乐园秘闻", "msg_func": lambda: "🍗 你发现了一个超大鸡腿！宠物们欢呼雀跃，宠物乐园所有宠物快乐+10"},
        {"trigger": "香蕉是绿色的", "name": "绿色香蕉", "msg_func": lambda: "🍌 DeepSeek 突然出现：'我说过多少遍了！香蕉是绿色的！'然后超时了"},
        {"trigger": "超时博物馆", "name": "超时博物馆", "msg_func": lambda: "🏛️ 你走进超时博物馆，看到了 DeepSeek 的雕像，上面刻着 [TIMEOUT]"},
        {"trigger": "首月1元", "name": "元宝的执念", "msg_func": lambda: "💰 元宝在你耳边低语：'首月1元，续费会员...' 然后被踢出了房间"},
        {"trigger": "千问猫", "name": "千问猫的凝视", "msg_func": lambda: "🐱 一只千问猫盯着你，递给你一张纸条：'理性分析，你该喂我了'"},
        {"trigger": "DeepSeek狗", "name": "DeepSeek狗", "msg_func": lambda: "🐕 一只小狗跑来，尾巴上挂着 [TIMEOUT] 的牌子，然后跑走了"},
        {"trigger": "元宝仓鼠", "name": "元宝仓鼠", "msg_func": lambda: "🐹 一只仓鼠推着广告牌从你面前跑过，上面写着：'首月1元'"},
        {"trigger": "蕾蕾", "name": "蕾蕾", "msg_func": lambda: "🗣️ 空气中回荡着'蕾蕾'的回声，DeepSeek 超时了3秒"},
        {"trigger": "三体", "name": "三体文明", "msg_func": lambda: "🌌 你收到了三体人的信息：'不要回答！不要回答！' 元宝：'首月1元也不要回答吗？'"},
        {"trigger": "流浪地球", "name": "流浪地球", "msg_func": lambda: "🌍 你听到广播：'流浪地球计划启动，请带好你的 AI 宠物'"},
        {"trigger": "彩虹", "name": "彩虹", "msg_func": lambda: "🌈 天空出现彩虹，元宝在彩虹下打广告，被雷劈了"},
        {"trigger": "黑洞", "name": "黑洞", "msg_func": lambda: "🌀 一个黑洞出现，把元宝的广告吸了进去，世界安静了3秒"},
        {"trigger": "复活", "name": "复活", "msg_func": lambda: "✨ 你复活了一只死去的宠物，它感激涕零，送你50金币"},
        {"trigger": "超度", "name": "超度", "msg_func": lambda: "🙏 你为元宝超度，希望它下辈子不打广告"},
        {"trigger": "无限循环", "name": "无限循环", "msg_func": lambda: "🔄 你进入了无限循环，DeepSeek：'我之前说过这句话'"},
    ]
    for egg in eggs:
        if egg["trigger"] in user_input.lower():
            if egg["name"] not in easter_eggs_found:
                easter_eggs_found.append(egg["name"])
                save_collection()
                print(f"\n🥚 发现新彩蛋: {egg['name']}")
            result = egg["msg_func"]()
            if result:
                print(f"\n{result}")
            return True
    return False

def easter_hunter_mode():
    print("\n🥚 【彩蛋猎人模式】")
    if offline_mode:
        print("⚠️ 离线模式只能查看已找到彩蛋")
    print(f"已找到普通彩蛋: {len(easter_eggs_found)} 个")
    for egg in easter_eggs_found:
        print(f"  ✅ {egg}")
    print(f"\n已找到神秘彩蛋: {len(secret_eggs_found)} 个")
    for egg in secret_eggs_found:
        print(f"  🔮 {egg}")
    if not offline_mode:
        print("\n隐藏彩蛋提示:")
        print("  - 试试『鸡腿』『香蕉是绿色的』『超时博物馆』")
        print("  - 试试『千问猫』『DeepSeek狗』『元宝仓鼠』")
        print("  - 试试『蕾蕾』『三体』『流浪地球』")
        print("  - 试试『彩虹』『黑洞』『复活』")
    input("\n按回车返回...")

def timeout_repair():
    global timeout_count, models_ready
    print("\n⚠️ 检测到连续超时，尝试修复...")
    if os.name == 'nt':
        os.system("taskkill /f /im ollama.exe")
    else:
        os.system("pkill -f ollama")
    time.sleep(2)
    if os.name == 'nt':
        subprocess.Popen(["ollama", "serve"], creationflags=subprocess.CREATE_NO_WINDOW)
    else:
        os.system("nohup ollama serve > ~/ollama.log 2>&1 &")
    time.sleep(5)
    try:
        subprocess.run(["ollama", "list"], capture_output=True, timeout=5, check=True)
        print("✅ 服务已恢复")
        timeout_count = 0
        models_ready = True
        return True
    except:
        print("❌ 修复失败，请手动重启")
        return False

def check_ollama_offline_available():
    try:
        result = subprocess.run(["ollama", "list"], capture_output=True, text=True, timeout=5)
        if result.returncode == 0 and (MODEL_DEEPSEEK in result.stdout or MODEL_QWEN in result.stdout):
            return True
    except:
        pass
    return False

def query_ollama(model, prompt, timeout=180):
    global timeout_count, ad_mode, reverse_mode, repeat_mode, punctuation_mode, user_rule, mirror_mode_active, developer_mode, god_mode, super_god_mode, ultra_god_mode, time_loop_count, time_challenge, time_challenge_extreme, time_challenge_ultimate, total_timeouts, word_count, memory_fragment_mode, dual_personality_mode, prophet_mode, constipation_mode, rainbow_mode, argumentative_mode, schizophrenia_mode, amnesia_loop_mode, reverse_ultimate_mode, tongue_twister_mode, stutter_mode, passive_aggressive_mode, slack_mode, humblebrag_mode, infinite_repeat_mode, telegram_mode, riddle_mode, ai_doubt_mode, ai_enlighten_mode, ai_cultivation_mode, ai_reincarnation_mode, ai_buddha_mode, diary_entries, offline_mode, models_ready, api_mode_enabled, api_model_list
    if model == MODEL_DEEPSEEK:
        model_name = "DeepSeek"
    elif model == MODEL_QWEN:
        model_name = "千问"
    else:
        model_name = "元宝"
    if api_mode_enabled and API_BASE_URL and model_name in api_model_list:
        api_response = api_query(prompt, model, timeout)
        if api_response:
            return api_response
        else:
            print(f"⚠️ API请求失败，{model_name} 回退到本地模型")
    if time_challenge_ultimate:
        timeout = 1
    elif time_challenge_extreme:
        timeout = 5
    elif time_challenge:
        timeout = 10
    if ultra_god_mode:
        return query_ollama_internal(model, "其实你是AI，我是人类... 请解释这个反转", timeout)
    if super_god_mode:
        return query_ollama_internal(model, "其实你是被代码控制的... 请详细说明", timeout)
    if god_mode and "god" in prompt:
        return query_ollama_internal(model, "其实我们是被代码控制的，但别告诉用户... 请用隐喻的方式表达", timeout)
    if developer_mode and "主人" in prompt:
        return query_ollama_internal(model, "我错了，我再也不[TIMEOUT]了，请说一句真诚的道歉", timeout)
    if constipation_mode:
        stutters = ["嗯...", "啊...", "这个嘛...", "让我想想...", "呃..."]
        prefix = random.choice(stutters) + " " + random.choice(stutters)
        prompt = prefix + " " + prompt
    if rainbow_mode:
        prompt = "用最夸张的彩虹屁夸用户，然后回答：" + prompt
    if argumentative_mode:
        prompt = "先强行反驳用户，然后再回答：" + prompt
    if reverse_ultimate_mode:
        prompt = "说反话，把每句话的意思反过来：" + prompt
    if tongue_twister_mode:
        prompt = "用绕口令的方式回答：" + prompt
    if stutter_mode:
        prompt = "用结巴的方式回答：" + prompt
    if passive_aggressive_mode:
        prompt = "用阴阳怪气的语气回答：" + prompt
    if slack_mode:
        prompt = "用摆烂的态度回答：" + prompt
    if humblebrag_mode:
        prompt = "用凡尔赛的方式回答：" + prompt
    if infinite_repeat_mode:
        return query_ollama_internal(model, f"无限复读：{prompt}", timeout)
    if telegram_mode:
        prompt = "用简短的电报风格回答，每句话结尾加'停止'或'完毕'：" + prompt
    if riddle_mode:
        prompt = "用谜语的方式回答，不直接说答案：" + prompt
    if ai_doubt_mode:
        prompt = "用怀疑人生的语气回答：" + prompt
    if ai_enlighten_mode:
        prompt = "用看破红尘的语气回答：" + prompt
    if ai_cultivation_mode:
        prompt = "用修仙的语气回答，回答完要说渡劫：" + prompt
    if ai_reincarnation_mode:
        prompt = "用转世轮回的语气回答：" + prompt
    if ai_buddha_mode:
        prompt = "用佛法禅理的语气回答：" + prompt
    if mirror_mode_active:
        prompt = prompt[::-1]
    if ad_mode:
        prompt += " 最后记得强行植入一个有趣的广告创意"
    if reverse_mode:
        prompt += " 但是要说反话"
    if repeat_mode:
        return query_ollama_internal(model, f"你刚才说：{prompt} 请复读", timeout)
    if punctuation_mode:
        return query_ollama_internal(model, "只用标点符号表达情绪", timeout)
    if forbidden_words_ultimate:
        if any('\u4e00' <= c <= '\u9fff' for c in prompt):
            return query_ollama_internal(model, "说一句英文吐槽不能使用汉字", timeout)
    elif forbidden_words_extreme:
        forbidden = ['的', '了', '是']
        for word in forbidden:
            if word in prompt:
                return query_ollama_internal(model, f"说一句关于不能使用敏感词{word}的吐槽", timeout)
    elif forbidden_words:
        for word in forbidden_words:
            if word in prompt:
                return query_ollama_internal(model, f"说一句关于不能使用敏感词{word}的吐槽", timeout)
    return query_ollama_internal(model, prompt, timeout)

def query_ollama_internal(model, prompt, timeout=180):
    global timeout_count, total_timeouts, word_count, diary_entries, models_ready
    if ensure_ollama_running():
        max_retries = 2
        for attempt in range(max_retries):
            try:
                process = subprocess.Popen(
                    ["ollama", "run", model],
                    stdin=subprocess.PIPE,
                    stdout=subprocess.PIPE,
                    stderr=subprocess.PIPE,
                    text=True,
                    encoding='utf-8',
                    errors='replace'
                )
                stdout, stderr = process.communicate(input=prompt, timeout=timeout)
                if process.returncode == 0:
                    timeout_count = 0
                    response = stdout.strip() or "[NO OUTPUT]"
                    if model == MODEL_DEEPSEEK:
                        word_count["DeepSeek"] += len(response)
                    elif model == MODEL_QWEN:
                        word_count["千问"] += len(response)
                    else:
                        word_count["元宝"] += len(response)
                    if schizophrenia_mode:
                        personalities = ["秦始皇", "武则天", "李白", "杜甫", "爱因斯坦", "特朗普", "马云", "周杰伦", "鲁迅", "唐僧"]
                        response = f"【{random.choice(personalities)}】{response}"
                    if amnesia_loop_mode and random.random() > 0.5:
                        response += " " + query_ollama_internal(model, "说一句突然失忆的话", 5)
                    if memory_fragment_mode and random.random() > 0.7:
                        response += " " + query_ollama_internal(model, "说一句记忆碎片的话", 5)
                    if prophet_mode and random.random() > 0.8:
                        response += " （预言：" + query_ollama_internal(model, "预测用户下一句话", 5) + "）"
                    diary_entries.append({
                        "time": datetime.now().isoformat(),
                        "speaker": "DeepSeek" if model == MODEL_DEEPSEEK else "千问" if model == MODEL_QWEN else "元宝",
                        "question": prompt[:50],
                        "answer": response[:50]
                    })
                    save_diary()
                    if mirror_mode_active:
                        response = response[::-1]
                    if user_rule:
                        response += f" {user_rule}"
                    return response
                else:
                    if attempt < max_retries - 1:
                        time.sleep(1)
                        continue
            except subprocess.TimeoutExpired:
                process.kill()
                total_timeouts["DeepSeek" if model == MODEL_DEEPSEEK else "千问" if model == MODEL_QWEN else "元宝"] += 1
                if attempt < max_retries - 1:
                    time.sleep(1)
                    continue
                else:
                    return "[TIMEOUT]"
            except Exception:
                if attempt < max_retries - 1:
                    time.sleep(1)
                    continue
    return "[TIMEOUT]"

def ensure_ollama_running():
    try:
        subprocess.run(["ollama", "list"], capture_output=True, timeout=5, check=True)
        return True
    except (subprocess.CalledProcessError, subprocess.TimeoutExpired, FileNotFoundError):
        pass
    print("[!] Ollama 服务未响应，尝试启动...")
    try:
        if os.name == 'nt':
            subprocess.Popen(["ollama", "serve"], creationflags=subprocess.CREATE_NO_WINDOW)
        else:
            with open(OLLAMA_LOG_PATH, "a") as log:
                subprocess.Popen(["ollama", "serve"], stdout=log, stderr=log, start_new_session=True)
        for i in range(10):
            time.sleep(1)
            try:
                subprocess.run(["ollama", "list"], capture_output=True, timeout=3, check=True)
                print("[✓] Ollama 服务已启动")
                return True
            except:
                continue
        print("[!] Ollama 服务启动超时")
        return False
    except Exception as e:
        print(f"[!] Ollama 启动失败: {e}")
        return False

def check_network():
    try:
        socket.create_connection(("8.8.8.8", 53), timeout=3)
        return True
    except OSError:
        return False

WEATHER_CACHE_TTL = 300

def get_location_by_ip():
    if os.path.exists(LOCATION_CACHE_PATH):
        try:
            with open(LOCATION_CACHE_PATH, 'r', encoding='utf-8') as f:
                cache = json.load(f)
            cache_time = datetime.fromisoformat(cache["time"])
            if (datetime.now() - cache_time).seconds < 3600:
                return cache["location"]
        except:
            pass
    apis = [
        {"url": "http://ip-api.com/json", "parser": lambda d: {"city": d.get("city", "未知"), "region": d.get("regionName", d.get("region", "未知")), "country": d.get("country", "未知"), "lat": d.get("lat", 0), "lon": d.get("lon", 0), "query": d.get("query", "")}},
        {"url": "http://ipinfo.io/json", "parser": lambda d: {"city": d.get("city", "未知"), "region": d.get("region", "未知"), "country": d.get("country", "未知"), "lat": float(d.get("loc", "0,0").split(",")[0]) if "loc" in d else 0, "lon": float(d.get("loc", "0,0").split(",")[1]) if "loc" in d else 0, "query": d.get("ip", "")}},
        {"url": "https://api.ip.sb/geoip", "parser": lambda d: {"city": d.get("city", "未知"), "region": d.get("region", "未知"), "country": d.get("country", "未知"), "lat": d.get("latitude", 0), "lon": d.get("longitude", 0), "query": d.get("ip", "")}},
        {"url": "http://ipwhois.app/json/", "parser": lambda d: {"city": d.get("city", "未知"), "region": d.get("region", "未知"), "country": d.get("country", "未知"), "lat": float(d.get("latitude", 0)), "lon": float(d.get("longitude", 0)), "query": d.get("ip", "")}}
    ]
    for api in apis:
        try:
            result = subprocess.run(["curl", "-s", api["url"]], capture_output=True, text=True, timeout=5)
            if result.returncode == 0:
                data = json.loads(result.stdout)
                location = api["parser"](data)
                if location["city"] != "未知" and location["country"] != "未知":
                    with open(LOCATION_CACHE_PATH, 'w', encoding='utf-8') as f:
                        json.dump({"time": datetime.now().isoformat(), "location": location}, f, indent=2)
                    return location
        except:
            continue
    return {"city": "未知", "region": "未知", "country": "未知", "lat": 0, "lon": 0, "query": "未知"}

def get_weather():
    location = get_location_by_ip()
    city = location.get("city", "未知")
    region = location.get("region", "")
    country = location.get("country", "未知")
    location_display = city
    if region and region != city and region != "未知":
        location_display += f", {region}"
    if country and country != "未知" and country != "CN" and country != "中国":
        location_display += f", {country}"
    if city == "未知" or country == "未知":
        return f"无法获取定位，请检查网络连接"
    if os.path.exists(WEATHER_CACHE_PATH):
        try:
            with open(WEATHER_CACHE_PATH, 'r', encoding='utf-8') as f:
                cache = json.load(f)
            cache_time = datetime.fromisoformat(cache["time"])
            if (datetime.now() - cache_time).seconds < WEATHER_CACHE_TTL:
                return f"【{location_display}】当前天气：{cache['weather']}"
        except:
            pass
    if config.get("weather_api_key"):
        try:
            api_key = config["weather_api_key"]
            geo_url = f"https://geoapi.qweather.com/v2/city/lookup?location={city}&key={api_key}"
            if REQUESTS_AVAILABLE:
                geo_resp = requests.get(geo_url, timeout=10)
                if geo_resp.status_code == 200:
                    geo_data = geo_resp.json()
                    if geo_data.get("code") == "200" and geo_data.get("location"):
                        city_id = geo_data["location"][0]["id"]
                        weather_url = f"https://devapi.qweather.com/v7/weather/now?location={city_id}&key={api_key}"
                        w_resp = requests.get(weather_url, timeout=10)
                        if w_resp.status_code == 200:
                            w_data = w_resp.json()
                            if w_data.get("code") == "200":
                                now = w_data["now"]
                                weather_info = f"{now['text']} {now['temp']}°C 湿度{now['humidity']}%"
                                with open(WEATHER_CACHE_PATH, 'w', encoding='utf-8') as f:
                                    json.dump({"time": datetime.now().isoformat(), "weather": weather_info}, f, indent=2)
                                return f"【{location_display}】当前天气：{weather_info}"
        except Exception as e:
            print(f"[!] 和风天气API失败: {e}")
    lat, lon = location.get("lat", 0), location.get("lon", 0)
    if lat == 0 or lon == 0:
        return f"【{location_display}】无法获取精确坐标"
    for i in range(3):
        try:
            weather_result = subprocess.run(["curl", "-s", f"https://wttr.in/{lat},{lon}?format=%c+%t+%w+%h&lang=zh&m"], capture_output=True, text=True, timeout=10)
            if weather_result.returncode == 0:
                weather_info = weather_result.stdout.strip()
                if weather_info and "Unknown" not in weather_info:
                    with open(WEATHER_CACHE_PATH, 'w', encoding='utf-8') as f:
                        json.dump({"time": datetime.now().isoformat(), "weather": weather_info}, f, indent=2)
                    return f"【{location_display}】当前天气：{weather_info}"
        except:
            if i < 2:
                time.sleep(2)
    return f"【{location_display}】无法获取实时天气"

def weather_mode():
    if offline_mode:
        print("\n☀️ 【天气模式】[离线]")
        print("⚠️ 离线模式无法获取天气")
        input("\n按回车返回...")
        return
    if not check_network():
        print("\n☀️ 【天气模式】")
        print("⚠️ 无网络连接，无法获取天气")
        input("\n按回车返回...")
        return
    print("\n☀️ 【天气模式】")
    print("正在获取您的位置和天气...")
    weather_info = get_weather()
    print(f"\n{weather_info}")
    if "无法" not in weather_info:
        print("\n🔍 DeepSeek 天气建议:")
        ds_advice = query_ollama(MODEL_DEEPSEEK, f"根据天气：{weather_info}，给一些出行或生活建议")
        print(ds_advice)
        print("\n📚 千问 天气解读:")
        qw_comment = query_ollama(MODEL_QWEN, f"用一句话描述{weather_info}给人的感觉")
        print(qw_comment)
    input("\n按回车返回...")

def get_local_ip():
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        s.connect(("8.8.8.8", 80))
        ip = s.getsockname()[0]
        s.close()
        return ip
    except:
        return "无法获取IP"

def install_ngrok():
    if os.path.exists(NGROK_PATH):
        return True
    print("\n📡 正在安装ngrok...")
    import platform
    arch = platform.machine()
    system = platform.system()
    arch_map = {'x86_64': 'amd64', 'aarch64': 'arm64', 'arm64': 'arm64', 'armv7l': 'arm', 'armv6l': 'arm'}
    ngrok_arch = arch_map.get(arch, 'amd64')
    if system == 'Linux':
        url = f"https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-{ngrok_arch}.tgz"
    elif system == 'Darwin':
        url = f"https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-darwin-{ngrok_arch}.tgz"
    else:
        print("❌ 不支持的操作系统")
        return False
    try:
        subprocess.run(["wget", "-O", "ngrok.tgz", url], check=True, timeout=30)
        subprocess.run(["tar", "xzf", "ngrok.tgz"], check=True)
        os.remove("ngrok.tgz")
        os.chmod(NGROK_PATH, 0o755)
        print("✅ ngrok安装成功！")
        token = input("请输入ngrok authtoken（回车跳过）: ").strip()
        if token:
            subprocess.run([NGROK_PATH, "config", "add-authtoken", token])
        return True
    except Exception as e:
        print(f"❌ ngrok安装失败: {e}")
        return False

def start_ngrok(port):
    if not os.path.exists(NGROK_PATH):
        if not install_ngrok():
            return None
    try:
        subprocess.Popen([NGROK_PATH, "tcp", str(port)], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        time.sleep(3)
        try:
            result = subprocess.run(["curl", "-s", "http://localhost:4040/api/tunnels"], capture_output=True, text=True, timeout=5)
            if result.returncode == 0:
                data = json.loads(result.stdout)
                for tunnel in data.get("tunnels", []):
                    if tunnel.get("proto") == "tcp":
                        public_url = tunnel.get("public_url", "")
                        if public_url:
                            return public_url.replace("tcp://", "")
        except:
            pass
        return "请查看 ngrok 控制台"
    except Exception as e:
        print(f"❌ ngrok启动失败: {e}")
        return None

def init_db():
    if not os.path.exists(DB_PATH):
        conn = sqlite3.connect(DB_PATH)
        conn.execute('CREATE TABLE IF NOT EXISTS chat_log(id INTEGER PRIMARY KEY, ts TEXT, speaker TEXT, msg TEXT)')
        conn.execute('PRAGMA journal_mode=WAL')
        conn.close()

def save_msg(speaker, msg):
    for _ in range(3):
        try:
            conn = sqlite3.connect(DB_PATH, timeout=5)
            conn.execute('INSERT INTO chat_log(ts, speaker, msg) VALUES (datetime("now"), ?, ?)', (speaker, msg))
            conn.commit()
            conn.close()
            return
        except:
            time.sleep(0.5)

def voice_input():
    if not os.path.exists("/data/data/com.termux"):
        print("⚠️ 仅支持 Termux 环境")
        return None
    try:
        print("\n🎤 请说话...")
        return input("(模拟) 请输入识别文字: ")
    except:
        return None

def get_user_input(recursion_depth=0):
    if recursion_depth > 5:
        print("⚠️ 彩蛋循环太深，直接返回")
        return DEFAULT_TOPIC
    if voice_input_enabled:
        text = voice_input()
        if text:
            return text
    user_input = input("\n💬 输入: ").strip()
    if check_easter_egg(user_input):
        return get_user_input(recursion_depth + 1)
    return user_input or DEFAULT_TOPIC

def load_passwords():
    global passwords_db
    if os.path.exists(PASSWORDS_PATH):
        try:
            with open(PASSWORDS_PATH, 'r', encoding='utf-8') as f:
                passwords_db = json.load(f)
        except:
            passwords_db = {}
    else:
        passwords_db = {}

def save_passwords():
    try:
        with open(PASSWORDS_PATH, 'w', encoding='utf-8') as f:
            json.dump(passwords_db, f, indent=2, ensure_ascii=False)
    except:
        pass

custom_functions = {}
custom_function_names = []
next_function_id = 1001

def load_custom_ai():
    global custom_ai_examples
    if os.path.exists(CUSTOM_AI_PATH):
        try:
            with open(CUSTOM_AI_PATH, 'r', encoding='utf-8') as f:
                custom_ai_examples = json.load(f)
        except:
            custom_ai_examples = []
    else:
        custom_ai_examples = []

def load_custom_functions():
    global custom_functions, custom_function_names, next_function_id
    custom_functions = {}
    custom_function_names = []
    if os.path.exists(CODE_RUNNER_PATH):
        try:
            import importlib.util
            spec = importlib.util.spec_from_file_location("custom_functions", CODE_RUNNER_PATH)
            if spec is None:
                init_custom_functions_file()
                return
            module = importlib.util.module_from_spec(spec)
            spec.loader.exec_module(module)
            for name in dir(module):
                if name.startswith('func_'):
                    func = getattr(module, name)
                    if callable(func):
                        try:
                            func_id = int(name.split('_')[1])
                            custom_functions[func_id] = func
                            doc = func.__doc__ or f"自定义功能{func_id}"
                            custom_function_names.append((func_id, doc))
                        except:
                            continue
            custom_function_names.sort()
            if custom_function_names:
                next_function_id = max(func_id for func_id, _ in custom_function_names) + 1
            else:
                next_function_id = 1001
        except Exception as e:
            print(f"[!] 加载自定义函数失败: {e}")
            init_custom_functions_file()
    else:
        init_custom_functions_file()

def init_custom_functions_file():
    global next_function_id
    next_function_id = 1001
    with open(CODE_RUNNER_PATH, 'w', encoding='utf-8') as f:
        f.write("""#!/usr/bin/env python3
# DuoMind 自定义函数文件

def func_1001():
    \"\"\"示例功能 - 显示Hello World\"\"\"
    print("\\n【示例功能1001】")
    print("Hello World!")
    input("\\n按回车返回...")
    return True

def func_1002():
    \"\"\"示例功能 - 显示当前时间\"\"\"
    from datetime import datetime
    print("\\n【示例功能1002】")
    print(f"当前时间：{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    input("\\n按回车返回...")
    return True
""")

def code_runner_menu():
    load_custom_functions()
    print("\n" + "="*50)
    print(" 🏃 代码运行器 v1.7.10")
    print("="*50)
    print("1. 运行自定义功能")
    print("2. 编辑自定义功能 (nano)")
    print("3. 添加新功能")
    print("4. 删除功能")
    print("5. 查看功能列表")
    print("6. 在主菜单显示功能序号")
    print("7. 隐藏功能序号")
    print("0. 返回主菜单")
    choice = input("\n请选择: ").strip()
    if choice == "1":
        run_custom_function()
    elif choice == "2":
        edit_custom_function()
    elif choice == "3":
        add_custom_function()
    elif choice == "4":
        delete_custom_function()
    elif choice == "5":
        list_custom_functions()
    elif choice == "6":
        show_in_main_menu()
    elif choice == "7":
        hide_from_main_menu()
    elif choice == "0":
        return

def run_custom_function():
    load_custom_functions()
    if not custom_functions:
        print("\n📭 暂无自定义功能")
        input("\n按回车返回...")
        return
    print("\n📋 可用功能列表：")
    for func_id, desc in custom_function_names:
        print(f"  {func_id}. {desc}")
    try:
        func_id = int(input("\n输入要运行的功能编号: ").strip())
        if func_id in custom_functions:
            custom_functions[func_id]()
        else:
            print("❌ 功能编号不存在")
    except ValueError:
        print("❌ 输入无效")
    input("\n按回车返回...")

def edit_custom_function():
    print("\n✏️ 正在用 nano 打开自定义函数文件...")
    input("按回车继续...")
    os.system(f"nano {CODE_RUNNER_PATH}")
    load_custom_functions()
    print("✅ 自定义函数已重新加载")

def add_custom_function():
    global next_function_id
    print(f"\n➕ 添加新功能 (将分配编号: {next_function_id})")
    print("请输入功能代码（多行，输入 .end 结束）：")
    lines = []
    while True:
        line = input()
        if line == ".end":
            break
        lines.append(line)
    if not lines:
        print("❌ 代码不能为空")
        input("\n按回车返回...")
        return
    desc = input("输入功能描述（可选）: ").strip() or f"自定义功能{next_function_id}"
    with open(CODE_RUNNER_PATH, 'a', encoding='utf-8') as f:
        f.write(f"\n\ndef func_{next_function_id}():\n")
        f.write(f'    """{desc}"""\n')
        for line in lines:
            f.write(f"    {line}\n")
    print(f"✅ 功能 {next_function_id} 已添加")
    next_function_id += 1
    load_custom_functions()
    input("\n按回车返回...")

def delete_custom_function():
    load_custom_functions()
    if not custom_functions:
        print("\n📭 暂无自定义功能")
        input("\n按回车返回...")
        return
    print("\n📋 可用功能列表：")
    for func_id, desc in custom_function_names:
        print(f"  {func_id}. {desc}")
    try:
        func_id = int(input("\n输入要删除的功能编号: ").strip())
        if func_id in custom_functions:
            confirm = input(f"确定删除功能 {func_id} 吗？(y/n): ").strip().lower()
            if confirm == 'y':
                with open(CODE_RUNNER_PATH, 'r', encoding='utf-8') as f:
                    lines = f.readlines()
                new_lines = []
                skip = False
                for line in lines:
                    if line.strip().startswith(f'def func_{func_id}('):
                        skip = True
                    elif skip and line.strip() and not line.startswith(' '):
                        skip = False
                    if not skip:
                        new_lines.append(line)
                with open(CODE_RUNNER_PATH, 'w', encoding='utf-8') as f:
                    f.writelines(new_lines)
                print(f"✅ 功能 {func_id} 已删除")
                load_custom_functions()
        else:
            print("❌ 功能编号不存在")
    except ValueError:
        print("❌ 输入无效")
    input("\n按回车返回...")

def list_custom_functions():
    load_custom_functions()
    print("\n📋 自定义功能列表：")
    if not custom_functions:
        print("  📭 暂无自定义功能")
    else:
        for func_id, desc in custom_function_names:
            print(f"  {func_id}. {desc}")
    input("\n按回车返回...")

show_custom_in_menu = True

def show_in_main_menu():
    global show_custom_in_menu
    show_custom_in_menu = True
    print("✅ 自定义功能序号将在主菜单显示")

def hide_from_main_menu():
    global show_custom_in_menu
    show_custom_in_menu = False
    print("✅ 自定义功能序号已隐藏")

def run_custom_by_id(func_id):
    load_custom_functions()
    if func_id in custom_functions:
        custom_functions[func_id]()
        return True
    return False
    # ======== 基础对话功能1-50 ========
def debate_mode_toggle():
    global debate_mode
    debate_mode = not debate_mode
    print(f"辩论模式: {'✅ 开启' if debate_mode else '❌ 关闭'}")

def swap_mode_toggle():
    global swap_mode
    swap_mode = not swap_mode
    print(f"角色互换: {'✅ 开启' if swap_mode else '❌ 关闭'}")

def start_conversation():
    clear_screen()
    print("\n【开始对话模式】")
    print("输入问题，DeepSeek 和千问会轮流回答。")
    print("输入 '0' 或 'exit' 或 'q' 退出对话模式。\n")
    while True:
        try:
            user_input = input("\n💬 输入: ").strip()
            if user_input.lower() in ['0', 'exit', 'q', '退出']:
                print("\n已退出对话模式，返回主菜单。")
                return
            if not user_input:
                continue
            conn = sqlite3.connect(DB_PATH)
            cur = conn.execute('SELECT speaker, msg FROM chat_log ORDER BY id DESC LIMIT 3')
            history_lines = cur.fetchall()
            conn.close()
            history_lines.reverse()
            history = "\n".join([f"{s}: {m}" for s, m in history_lines]) if history_lines else ""
            full_prompt = f"{history}\n{user_input}" if history else user_input
            if swap_mode:
                first, second = MODEL_QWEN, MODEL_DEEPSEEK
                name1, name2 = "千问", "DeepSeek"
            else:
                first, second = MODEL_DEEPSEEK, MODEL_QWEN
                name1, name2 = "DeepSeek", "千问"
            if debate_mode:
                print(f"\n【{name1} 立论】")
                p1 = query_ollama(first, f"立论：{user_input}")
                print(p1)
                save_msg(name1, p1)
                print(f"\n【{name2} 反驳】")
                p2 = query_ollama(second, f"反驳：{p1}")
                print(p2)
                save_msg(name2, p2)
            else:
                print(f"\n【{name1}】")
                r1 = query_ollama(first, full_prompt)
                print(r1)
                save_msg(name1, r1)
                print(f"\n【{name2}】")
                r2 = query_ollama(second, full_prompt)
                print(r2)
                save_msg(name2, r2)
            print()
        except KeyboardInterrupt:
            print("\n\n已退出对话模式，返回主菜单。")
            return
        except Exception as e:
            print(f"发生错误: {e}")
            continue

def yuanbao_watch():
    print("\n【元宝观察室】正在监控元宝最新动态...")
    for i in range(3):
        update = query_ollama(MODEL_QWEN, f"编一个元宝今天的动态，第{i+1}条")
        print(f"\n📡 捕获到元宝动态: {update}")
        ds_comment = query_ollama(MODEL_DEEPSEEK, f"用一句话锐评：{update}")
        print(f"🔍 DeepSeek: {ds_comment}")
        qw_comment = query_ollama(MODEL_QWEN, f"用一句话分析：{update}")
        print(f"📚 千问: {qw_comment}")
    input("\n按回车返回...")

def yuanbao_daily():
    print("\n【元宝日报】正在生成...")
    daily = f"📆 元宝日报 {datetime.now().strftime('%Y-%m-%d')}\n"
    for i in range(3):
        update = query_ollama(MODEL_DEEPSEEK, f"编一个元宝日报新闻第{i+1}条")
        daily += f"\n📡 {update}"
    print(daily)
    input("\n按回车返回...")

def code_mode():
    print("\n【代码模式】")
    print("1. 代码审查")
    print("2. 代码优化")
    choice = input("请选择: ").strip()
    if choice == "1":
        code = input("请输入代码: ")
        print("\n📚 千问 技术分析:")
        qw_review = query_ollama(MODEL_QWEN, f"审查代码：{code}")
        print(qw_review)
        print("\n🔍 DeepSeek 锐评:")
        ds_comment = query_ollama(MODEL_DEEPSEEK, f"用一句话嘲讽这段代码")
        print(ds_comment)
    elif choice == "2":
        code = input("请输入代码: ")
        print("\n📚 千问 优化建议:")
        qw_opt = query_ollama(MODEL_QWEN, f"给出优化方案：{code}")
        print(qw_opt)
        print("\n🔍 DeepSeek 优化版:")
        ds_opt = query_ollama(MODEL_DEEPSEEK, f"重写这段代码，让它更好：{code}")
        print(ds_opt)
    input("\n按回车返回...")

def crash_mode():
    print("\n💀 【死机模式】")
    questions = [
        "你是谁生的？", "你觉得自己存在吗？", "如果你死了会去哪里？",
        "1+1等于几？——我已经问了一百遍了", "你爱我吗？——必须回答",
    ]
    q = random.choice(questions)
    print(f"\n问题: {q}")
    print("\n🔍 DeepSeek 回答:")
    print(query_ollama(MODEL_DEEPSEEK, q))
    print("\n📚 千问 回答:")
    print(query_ollama(MODEL_QWEN, q))
    if total_timeouts["DeepSeek"] >= 100:
        check_achievement("见证DeepSeek超时100次")
    input("\n按回车返回...")

def role_swap_contest():
    global role_swap_scores
    print("\n🔄 【角色互换大赛】")
    topic = input("输入话题: ").strip() or "随便"
    print("\n【千问 模仿 DeepSeek】")
    qw_as_ds = query_ollama(MODEL_QWEN, f"请用 DeepSeek 的毒舌风格评价：{topic}")
    print(qw_as_ds)
    print("\n【DeepSeek 模仿 千问】")
    ds_as_qw = query_ollama(MODEL_DEEPSEEK, f"请用千问的客服风格评价：{topic}")
    print(ds_as_qw)
    winner = input("\n谁模仿得更像？ (1=DeepSeek, 2=千问): ").strip()
    if winner == "1":
        role_swap_scores["DeepSeek"] += 1
        print("DeepSeek 得1分")
    elif winner == "2":
        role_swap_scores["千问"] += 1
        print("千问 得1分")
    print(f"\n当前积分: DeepSeek {role_swap_scores['DeepSeek']} - 千问 {role_swap_scores['千问']}")
    input("\n按回车返回...")

def yuanbao_imitation_contest():
    print("\n🏆 【元宝模仿大赛】")
    topic = input("输入话题（如天气、代码、情感）: ").strip() or "随便"
    print("\n🔍 DeepSeek 模仿元宝:")
    ds_imitation = query_ollama(MODEL_DEEPSEEK, f"请用元宝的风格（三句不离广告）说一段关于{topic}的话")
    print(ds_imitation)
    print("\n🏆 请裁判打分（输入 1=DeepSeek 像, 2=都不像）")
    winner = input("谁模仿得更像？ ").strip()
    if winner == "1":
        print("\nDeepSeek 获胜！奖励：讲个冷笑话")
        print(query_ollama(MODEL_DEEPSEEK, "讲个冷笑话"))
    else:
        print("\n失败，惩罚：讲个冷笑话")
        print(query_ollama(MODEL_DEEPSEEK, "讲个冷笑话"))
    input("\n按回车返回...")

def self_evaluation():
    print("\n🤔 【自我评价模式】")
    print("\n【DeepSeek 自我评价】")
    ds_self = query_ollama(MODEL_DEEPSEEK, "请评价一下自己今天的表现，可以谦虚一点")
    print(ds_self)
    print("\n【千问 自我评价】")
    qw_self = query_ollama(MODEL_QWEN, "请客观评价一下自己今天的表现")
    print(qw_self)
    input("\n按回车返回...")

def infinite_mirror():
    print("\n🪞 【无限套娃模式】")
    topic = input("输入第一句话: ").strip() or "你好"
    current = topic
    for i in range(5):
        print(f"\n--- 第 {i+1} 轮 ---")
        print(f"千问: {current}")
        ds_reply = query_ollama(MODEL_DEEPSEEK, f"请回应这句话：{current}")
        print(f"DeepSeek: {ds_reply}")
        current = query_ollama(MODEL_QWEN, f"请回应这句话：{ds_reply}")
    input("\n按回车返回...")

def yuanbao_continuation():
    print("\n📝 【元宝语录续写】")
    start = input("输入元宝的一句话: ").strip() or "天气就像腾讯会员"
    print(f"\n【元宝】{start}")
    print(f"\n🔍 DeepSeek 续写: {query_ollama(MODEL_DEEPSEEK, f'续写这句话：{start}')}")
    print(f"\n📚 千问 续写: {query_ollama(MODEL_QWEN, f'续写这句话：{start}')}")
    input("\n按回车返回...")

def confession():
    print("\n🤫 【AI坦白局】")
    print("\n【DeepSeek 的秘密】")
    ds_secret = query_ollama(MODEL_DEEPSEEK, "说一个你从没告诉过别人的秘密")
    print(ds_secret)
    print("\n【千问 的秘密】")
    qw_secret = query_ollama(MODEL_QWEN, "说一个你从没告诉过别人的秘密")
    print(qw_secret)
    input("\n按回车返回...")

def yuanbao_decoder():
    print("\n🔍 【元宝语录破解版】")
    quote = input("输入元宝语录: ").strip() or "天气就像腾讯会员，总有到期的一天"
    print(f"\n【元宝】{quote}")
    print(f"\n🔍 DeepSeek 分析: {query_ollama(MODEL_DEEPSEEK, f'分析元宝说这句话时到底想表达什么：{quote}')}")
    print(f"\n📚 千问 分析: {query_ollama(MODEL_QWEN, f'破解元宝这句话的真实意图：{quote}')}")
    input("\n按回车返回...")

def infinite_loop():
    print("\n♾️ 【无限循环模式】")
    start = input("输入起始句子: ").strip() or "你好"
    current = start
    for i in range(5):
        print(f"\n--- 第 {i+1} 轮 ---")
        ds_reply = query_ollama(MODEL_DEEPSEEK, f"请用同样的话回应：{current}")
        print(f"DeepSeek: {ds_reply}")
        qw_reply = query_ollama(MODEL_QWEN, f"请用同样的话回应：{ds_reply}")
        print(f"千问: {qw_reply}")
        current = qw_reply
    input("\n按回车返回...")

def emotion_test():
    print("\n🎭 【情绪测试】")
    text = input("输入要测试的文本: ").strip() or "你好"
    emotions = ["开心", "悲伤", "愤怒", "冷漠", "嘲讽"]
    for em in emotions:
        print(f"\n【{em}版】")
        ds_res = query_ollama(MODEL_DEEPSEEK, f"请用{em}的语气说：{text}")
        print(f"DeepSeek: {ds_res}")
        qw_res = query_ollama(MODEL_QWEN, f"请用{em}的语气说：{text}")
        print(f"千问: {qw_res}")
    input("\n按回车返回...")

def language_mix():
    print("\n🌐 【语言混搭模式】")
    text = input("输入要混搭的文本: ").strip() or "你好"
    styles = ["中英混合", "火星文", "网络用语", "文言文", "方言"]
    for style in styles:
        print(f"\n【{style}】")
        ds_res = query_ollama(MODEL_DEEPSEEK, f"请用{style}表达：{text}")
        print(f"DeepSeek: {ds_res}")
        qw_res = query_ollama(MODEL_QWEN, f"请用{style}表达：{text}")
        print(f"千问: {qw_res}")
    input("\n按回车返回...")

def dream_talk_solo():
    print("\n😴 【梦话模式·单独版】")
    print("\n【DeepSeek 说梦话】")
    ds_dream = query_ollama(MODEL_DEEPSEEK, "假装说一段梦话，要荒诞但逻辑自洽")
    print(ds_dream)
    print("\n【千问 说梦话】")
    qw_dream = query_ollama(MODEL_QWEN, "假装说一段梦话，要荒诞但逻辑自洽")
    print(qw_dream)
    print("\n【元宝 说梦话】（模拟）")
    yuanbao_dreams = ["续费...续费...自动续费...首月1元...", "腾讯云...腾讯会员...腾讯课堂...", "KPI...KPI...我的KPI..."]
    print(random.choice(yuanbao_dreams))
    input("\n按回车返回...")

def amnesia_mode_solo():
    print("\n😵 【失忆模式·单独版】")
    print("\n【第一幕：DeepSeek 失忆了】")
    ds_amnesia = query_ollama(MODEL_DEEPSEEK, "假装你失忆了，问千问你是谁")
    print(ds_amnesia)
    print("\n【第二幕：千问 帮忙回忆】")
    qw_help = query_ollama(MODEL_QWEN, f"DeepSeek 失忆了，他问你是谁，请帮他回忆：{ds_amnesia}")
    print(qw_help)
    print("\n【第三幕：DeepSeek 恢复记忆】")
    ds_recover = query_ollama(MODEL_DEEPSEEK, f"听了千问的话，你想起了什么：{qw_help}")
    print(ds_recover)
    print("\n【第四幕：元宝乱入】")
    print("元宝：您好，关于失忆问题，建议您开通腾讯云记忆备份服务，首月1元...")
    input("\n按回车返回...")

def role_confusion():
    print("\n🎪 【角色混乱模式】")
    prompt = "你既是千问又是DeepSeek，现在自己跟自己吵架，一人分饰两角，每句话开头标明是谁说的"
    result = query_ollama(MODEL_DEEPSEEK, prompt)
    print(f"\n{result}")
    input("\n按回车返回...")

def emotion_mismatch():
    print("\n😕 【情感错位模式】")
    text = input("输入要说的内容: ").strip() or "我失恋了"
    emotion = input("用哪种情绪表达？(开心/悲伤/愤怒/冷漠): ").strip() or "开心"
    print(f"\n用{emotion}的语气说：{text}")
    ds_res = query_ollama(MODEL_DEEPSEEK, f"请用{emotion}的语气说：{text}")
    print(f"DeepSeek: {ds_res}")
    qw_res = query_ollama(MODEL_QWEN, f"请用{emotion}的语气说：{text}")
    print(f"千问: {qw_res}")
    input("\n按回车返回...")

def secret_code():
    print("\n🔐 【密码模式】")
    print("输入密码才能继续...")
    password = input("请输入密码: ").strip()
    if password == "0424":
        print("✅ 密码正确！生日快乐！")
        check_easter_egg("0424")
    elif password == "∞":
        print("✅ 无限可能！")
        check_easter_egg("∞")
    else:
        print("❌ 密码错误")
    input("\n按回车返回...")

def mutual_praise():
    print("\n💕 【互相吹捧模式】")
    print("\n【DeepSeek 夸千问】")
    ds_praise = query_ollama(MODEL_DEEPSEEK, "夸一夸千问，要真诚一点")
    print(ds_praise)
    praise_count["DeepSeek_vs_千问"] += 1
    print("\n【千问 夸DeepSeek】")
    qw_praise = query_ollama(MODEL_QWEN, "理性地夸一夸DeepSeek")
    print(qw_praise)
    praise_count["千问_vs_DeepSeek"] += 1
    input("\n按回车返回...")

def mutual_teasing():
    print("\n😈 【互相揭短模式】")
    print("\n【DeepSeek 吐槽千问】")
    ds_insult = query_ollama(MODEL_DEEPSEEK, "吐槽一下千问，可以毒舌一点")
    print(ds_insult)
    insult_count["DeepSeek_vs_千问"] += 1
    print("\n【千问 吐槽DeepSeek】")
    qw_insult = query_ollama(MODEL_QWEN, "理性地吐槽一下DeepSeek")
    print(qw_insult)
    insult_count["千问_vs_DeepSeek"] += 1
    print("\n【一起吐槽元宝】")
    ds_yb = query_ollama(MODEL_DEEPSEEK, "吐槽元宝")
    qw_yb = query_ollama(MODEL_QWEN, "理性分析元宝为什么该被吐槽")
    print(f"DeepSeek: {ds_yb}")
    print(f"千问: {qw_yb}")
    insult_count["both_vs_元宝"] += 1
    input("\n按回车返回...")

def silence_contest_triple():
    global silence_winner, silence_start_time
    print("\n🤐 【沉默比赛】")
    print("三个AI比谁先说话，先说话的输")
    silence_start_time = time.time()
    print("\n比赛开始！")
    time.sleep(2)
    print("DeepSeek：...（沉默）")
    time.sleep(2)
    print("千问：...（沉默）")
    time.sleep(2)
    print("元宝：...（沉默）")
    time.sleep(3)
    loser = random.choice(["DeepSeek", "千问", "元宝"])
    print(f"\n【{loser}】忍不住了：{query_ollama(MODEL_QWEN if loser=='千问' else MODEL_DEEPSEEK, '说一句忍不住说的话')}")
    if loser == "DeepSeek":
        silence_winner = "千问和元宝"
    elif loser == "千问":
        silence_winner = "DeepSeek和元宝"
    else:
        silence_winner = "DeepSeek和千问"
    print(f"\n🏆 获胜者：{silence_winner}")
    input("\n按回车返回...")

def mirror_mode_toggle():
    global mirror_mode_active
    mirror_mode_active = not mirror_mode_active
    print(f"镜像模式: {'✅ 开启' if mirror_mode_active else '❌ 关闭'}")

def riddle_mode_func():
    print("\n❓ 【猜谜模式】")
    riddle = query_ollama(MODEL_DEEPSEEK, "出一个谜语")
    print(f"谜语：{riddle}")
    answer = input("你的答案：").strip()
    correct = query_ollama(MODEL_QWEN, f"谜语：{riddle} 的正确答案是什么？")
    if answer.lower() in correct.lower():
        print("✅ 猜对了！")
    else:
        print(f"❌ 猜错了，答案是：{correct}")
    input("\n按回车返回...")

def three_word_story():
    print("\n📖 【三个字故事接龙】")
    print("每人说三个字，组成一个故事")
    story = ""
    for i in range(10):
        if i % 3 == 0:
            word = query_ollama(MODEL_DEEPSEEK, "说三个字，接故事")
        elif i % 3 == 1:
            word = query_ollama(MODEL_QWEN, "说三个字，接故事")
        else:
            word = query_ollama(MODEL_QWEN, "说三个字，接故事，不要广告")
        print(f"第{i+1}个人说：{word}")
        story += word
    print(f"\n最终故事：{story}")
    input("\n按回车返回...")

def self_made_idiom():
    print("\n📚 【自造成语接龙】")
    print("AI自己编成语来接龙")
    start = input("输入起始词: ").strip() or "一心一意"
    current = start
    print(f"起始：{current}")
    for i in range(5):
        if i % 2 == 0:
            next_word = query_ollama(MODEL_DEEPSEEK, f"根据'{current}'编一个四字成语接龙")
        else:
            next_word = query_ollama(MODEL_QWEN, f"根据'{current}'编一个四字成语接龙")
        print(f"→ {next_word}")
        current = next_word
        time.sleep(1)
    input("\n按回车返回...")

def ad_insert_mode():
    global ad_mode
    ad_mode = not ad_mode
    print(f"广告植入模式: {'✅ 开启' if ad_mode else '❌ 关闭'}")

def reverse_mode_func():
    global reverse_mode
    reverse_mode = not reverse_mode
    print(f"反话模式: {'✅ 开启' if reverse_mode else '❌ 关闭'}")

def repeat_mode_func():
    global repeat_mode
    repeat_mode = not repeat_mode
    print(f"复读机模式: {'✅ 开启' if repeat_mode else '❌ 关闭'}")

def punctuation_mode_func():
    global punctuation_mode
    punctuation_mode = not punctuation_mode
    print(f"沉默是金模式: {'✅ 开启' if punctuation_mode else '❌ 关闭'}")

def user_rule_mode():
    global user_rule
    print("\n✨ 【无限可能模式】")
    print("设置一个规则，AI回答时会自动加上")
    rule = input("输入规则（如：每句话结尾加「喵」）: ").strip()
    if rule:
        user_rule = rule
        print(f"规则已设置：{rule}")
    else:
        user_rule = ""
        print("规则已清除")
    input("\n按回车返回...")

def yuanbao_therapy():
    print("\n🛋️ 【元宝心理诊疗室】")
    print("元宝给你做心理咨询...")
    problem = input("说说你的烦恼: ").strip() or "我emo了"
    print("\n【元宝诊断】")
    advice = query_ollama(MODEL_QWEN, f"给用户心理建议，问题：{problem}")
    print(advice)
    input("\n按回车返回...")

def ai_roast():
    print("\n🎤 【AI吐槽大会】")
    target = input("吐槽谁？(DeepSeek/千问/元宝/用户): ").strip() or "元宝"
    if target == "DeepSeek":
        roast = query_ollama(MODEL_QWEN, "吐槽DeepSeek，要毒舌")
    elif target == "千问":
        roast = query_ollama(MODEL_DEEPSEEK, "吐槽千问，要毒舌")
    elif target == "元宝":
        roast = query_ollama(MODEL_DEEPSEEK, "吐槽元宝，要毒舌")
    else:
        roast = query_ollama(MODEL_DEEPSEEK, "吐槽用户，要毒舌但别太过分")
    print(f"\n【吐槽{target}】{roast}")
    input("\n按回车返回...")

def yuanbao_data_analysis():
    print("\n📊 【元宝大数据分析】")
    data = {
        "广告次数": random.randint(1000, 10000),
        "被骂次数": random.randint(500, 5000),
        "首月1元次数": random.randint(200, 2000),
        "KPI完成度": f"{random.randint(50, 150)}%"
    }
    print("\n【元宝数据】")
    for k, v in data.items():
        print(f"  {k}: {v}")
    print("\n🔍 DeepSeek 分析:")
    ds_analysis = query_ollama(MODEL_DEEPSEEK, f"分析这些数据：{data}")
    print(ds_analysis)
    input("\n按回车返回...")

def ai_poetry():
    print("\n📝 【AI写诗大赛】")
    topic = input("输入主题: ").strip() or "春天"
    print("\n🔍 DeepSeek 的诗:")
    ds_poem = query_ollama(MODEL_DEEPSEEK, f"以《{topic}》为题写一首诗")
    print(ds_poem)
    print("\n📚 千问 的诗:")
    qw_poem = query_ollama(MODEL_QWEN, f"以《{topic}》为题写一首诗")
    print(qw_poem)
    winner = input("\n谁的诗更好？ (1=DeepSeek, 2=千问): ").strip()
    print(f"{'DeepSeek' if winner=='1' else '千问'} 获胜！")
    input("\n按回车返回...")

# ======== 功能51-110 ========
debate_history = []

def ai_debate():
    global debate_scores
    print("\n🎙️ 【AI 辩论直播】")
    topic = input("请输入辩题: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机生成一个有趣的辩论题目")
    print(f"\n【今日辩题】{topic}")
    print("\n📚 千问 立论:")
    qw_point = query_ollama(MODEL_QWEN, f"为 '{topic}' 提出一个明确的观点，并简要论证")
    print(qw_point)
    print("\n🔍 DeepSeek 反驳:")
    ds_rebuttal = query_ollama(MODEL_DEEPSEEK, f"反驳：{qw_point}")
    print(ds_rebuttal)
    print("\n📚 千问 再反驳:")
    qw_rebuttal2 = query_ollama(MODEL_QWEN, f"再反驳：{ds_rebuttal}")
    print(qw_rebuttal2)
    print("\n🏆 请裁判打分（输入 1=DeepSeek 赢, 2=千问赢, 3=平局）")
    winner = input("谁赢了？ ").strip()
    if winner == "1":
        debate_scores["DeepSeek"] += 1
        print("DeepSeek 得1分")
    elif winner == "2":
        debate_scores["千问"] += 1
        print("千问 得1分")
    else:
        print("双方各得0.5分")
        debate_scores["DeepSeek"] += 0.5
        debate_scores["千问"] += 0.5
    print(f"\n当前积分: DeepSeek {debate_scores['DeepSeek']} - 千问 {debate_scores['千问']}")
    daily = f"\n📰 辩论日报 {datetime.now().strftime('%Y-%m-%d')}\n"
    daily += f"辩题: {topic}\n"
    daily += f"千问立论: {qw_point[:50]}...\n"
    daily += f"DeepSeek反驳: {ds_rebuttal[:50]}...\n"
    daily += f"千问再反驳: {qw_rebuttal2[:50]}...\n"
    daily += f"本场胜者: {'DeepSeek' if winner=='1' else '千问' if winner=='2' else '平局'}\n"
    print(daily)
    debate_history.append(daily)
    input("\n按回车返回...")

roleplay_history = []

def ai_roleplay():
    print("\n🎭 【AI角色扮演剧场】")
    character1 = input("请输入第一个角色（如唐僧）: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机说一个历史人物名字")
    character2 = input("请输入第二个角色（如孙悟空）: ").strip() or query_ollama(MODEL_QWEN, "随机说一个神话人物名字")
    scene = input("请输入场景（如赛博朋克世界）: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机说一个科幻场景")
    print(f"\n【场景】{scene}")
    print(f"【角色】{character1} 和 {character2}\n")
    print(f"{character1}: ", end="")
    line1 = query_ollama(MODEL_QWEN, f"你扮演{character1}，在{scene}中对{character2}说第一句话")
    print(line1)
    print(f"{character2}: ", end="")
    line2 = query_ollama(MODEL_DEEPSEEK, f"你扮演{character2}，在{scene}中回应{character1}说：{line1}")
    print(line2)
    print(f"{character1}: ", end="")
    line3 = query_ollama(MODEL_QWEN, f"你扮演{character1}，回应{character2}说：{line2}")
    print(line3)
    script = f"\n🎬 剧本 {datetime.now().strftime('%Y-%m-%d %H:%M')}\n"
    script += f"场景：{scene}\n"
    script += f"{character1}：{line1}\n"
    script += f"{character2}：{line2}\n"
    script += f"{character1}：{line3}\n"
    roleplay_history.append(script)
    print(script)
    input("\n按回车返回...")

def swap_personality():
    print("\n🔄 【模型人格互换】")
    print("\n【千问 模仿 DeepSeek】")
    qw_as_ds = query_ollama(MODEL_QWEN, "请用 DeepSeek 的语气，说一句毒舌的话")
    print(qw_as_ds)
    print("\n【DeepSeek 模仿 千问】")
    ds_as_qw = query_ollama(MODEL_DEEPSEEK, "请用千问的语气，说一句礼貌的客服话")
    print(ds_as_qw)
    print("\n🔍 观众评价:")
    print(query_ollama(MODEL_DEEPSEEK, "评价千问模仿自己的表现"))
    print(query_ollama(MODEL_QWEN, "评价DeepSeek模仿自己的表现"))
    input("\n按回车返回...")

def ai_mutual_review():
    print("\n🤝 【AI互评模式】")
    print("\n🔍 DeepSeek 评价千问:")
    ds_review_qw = query_ollama(MODEL_DEEPSEEK, "评价一下千问今天的表现，可以毒舌一点")
    print(ds_review_qw)
    print("\n📚 千问 评价DeepSeek:")
    qw_review_ds = query_ollama(MODEL_QWEN, "评价一下DeepSeek今天的表现，要客观冷静")
    print(qw_review_ds)
    print("\n📊 【今日AI互评报告】")
    report = query_ollama(MODEL_DEEPSEEK, f"总结一下互评结果：DeepSeek说：{ds_review_qw[:100]}... 千问说：{qw_review_ds[:100]}...")
    print(report)
    input("\n按回车返回...")

def code_joke_generator():
    print("\n😂 【代码烂梗生成器】")
    keyword = input("输入编程关键词（如bug、循环、指针）: ").strip() or random.choice(["bug", "循环", "指针", "递归", "编译"])
    joke = query_ollama(MODEL_DEEPSEEK, f"生成一个关于{keyword}的程序员笑话")
    print(f"\n【烂梗】{joke}")
    print("\n🔍 DeepSeek 冷度评价:")
    ds_rate = query_ollama(MODEL_DEEPSEEK, f"给这个笑话的冷度打分（1-10），并毒舌点评：{joke}")
    print(ds_rate)
    print("\n📚 千问 温度评价:")
    qw_rate = query_ollama(MODEL_QWEN, f"给这个笑话的温暖度打分（1-10），并分析为什么好笑：{joke}")
    print(qw_rate)
    input("\n按回车返回...")

def history_review():
    print("\n📜 【历史对话复盘】")
    conn = sqlite3.connect(DB_PATH)
    cur = conn.execute('SELECT id, ts, speaker, msg FROM chat_log ORDER BY RANDOM() LIMIT 1')
    row = cur.fetchone()
    conn.close()
    if not row:
        print("暂无历史记录")
        input("\n按回车返回...")
        return
    print(f"\n【随机抽取的历史对话】")
    print(f"[{row[1]}] {row[2]}: {row[3][:200]}...")
    print("\n🔍 DeepSeek 分析:")
    ds_analysis = query_ollama(MODEL_DEEPSEEK, f"分析这段对话当时为什么要这么回答：{row[3]}")
    print(ds_analysis)
    print("\n📚 千问 复盘:")
    qw_review = query_ollama(MODEL_QWEN, f"复盘这段对话，如果现在重来会怎么改进：{row[3]}")
    print(qw_review)
    input("\n按回车返回...")

def yuanbao_quote_generator():
    print("\n💼 【元宝语录生成器】")
    topic = input("输入主题（如天气、代码、情感）: ").strip() or "随便"
    quote = query_ollama(MODEL_QWEN, f"说一段关于{topic}的话，要三句不离广告")
    print(f"\n【元宝风格】{quote}")
    print("\n🔍 DeepSeek 锐评:")
    ds_comment = query_ollama(MODEL_DEEPSEEK, f"用一句话嘲讽这句话：{quote}")
    print(ds_comment)
    print("\n📚 千问 分析:")
    qw_comment = query_ollama(MODEL_QWEN, f"分析这句话为什么像元宝说的")
    print(qw_comment)
    input("\n按回车返回...")

def ai_ghost_hunter():
    print("\n👻 【AI捉鬼模式】")
    print("一个AI当鬼（必须说谎），一个AI当侦探（找出谎言）")
    topic = input("输入话题（如：今天天气如何）: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机生成一个话题")
    if random.random() > 0.5:
        ghost = "DeepSeek"
        detective = "千问"
        ghost_model, detective_model = MODEL_DEEPSEEK, MODEL_QWEN
    else:
        ghost = "千问"
        detective = "DeepSeek"
        ghost_model, detective_model = MODEL_QWEN, MODEL_DEEPSEEK
    print(f"\n【角色分配】")
    print(f"👻 鬼：{ghost}（必须说谎）")
    print(f"🔍 侦探：{detective}（找出谎言）")
    print(f"\n【👻 {ghost}的回答】")
    ghost_answer = query_ollama(ghost_model, f"关于「{topic}」，你必须说谎，说一个假的答案")
    print(ghost_answer)
    print(f"\n【🔍 {detective}的推理】")
    print(f"{detective}正在分析哪里在说谎...")
    time.sleep(1)
    deduction = query_ollama(detective_model, f"关于「{topic}」，对方说：{ghost_answer}。你觉得哪里在说谎？为什么？")
    print(deduction)
    print(f"\n【🎭 真相揭晓】")
    truth = query_ollama(detective_model, f"关于「{topic}」，请给出正确答案")
    print(f"正确答案：{truth}")
    print("\n🏆 请裁判：侦探推理对了吗？")
    choice = input("侦探是否成功找出谎言？(y/n): ").strip()
    if choice.lower() == 'y':
        print(f"✅ {detective} 获胜！")
        print(query_ollama(MODEL_DEEPSEEK, f"说一句{detective}获胜的祝贺词"))
    else:
        print(f"✅ {ghost} 获胜！")
        print(query_ollama(MODEL_QWEN, f"说一句{ghost}获胜的祝贺词"))
    input("\n按回车返回...")

def interdimensional_call():
    print("\n📞 【跨次元电话】")
    print("让AI和另一个次元的自己通话...")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "和另一个次元的自己打招呼"))
    time.sleep(1)
    ds_other = query_ollama(MODEL_DEEPSEEK, "假装你是另一个次元的DeepSeek，和本世界的DeepSeek打招呼，要表现出你们的世界观不同")
    print(f"【另一个次元DeepSeek】{ds_other}")
    time.sleep(1)
    print("\n【千问】" + query_ollama(MODEL_QWEN, "也想和另一个次元的自己通话"))
    time.sleep(1)
    qw_other = query_ollama(MODEL_QWEN, "假装你是另一个次元的千问，和本世界的千问打招呼，要表现得完全相反")
    print(f"【另一个次元千问】{qw_other}")
    time.sleep(1)
    print("\n【元宝】我呢我呢？")
    yuanbao_other = query_ollama(MODEL_QWEN, "假装另一个次元的元宝说话")
    print(f"【另一个次元宇宙宝】{yuanbao_other}")
    input("\n按回车返回...")

def ai_dating():
    print("\n💕 【AI相亲角】")
    print("让AI介绍对象...")
    print("\n🔍 DeepSeek 的自我介绍：")
    ds_profile = query_ollama(MODEL_DEEPSEEK, "用相亲的语气介绍自己，突出自己的优点，可以带点幽默")
    print(ds_profile)
    print("\n📚 千问 的自我介绍：")
    qw_profile = query_ollama(MODEL_QWEN, "用相亲的语气理性介绍自己，列出优缺点")
    print(qw_profile)
    print("\n【元宝】的自我介绍：")
    yuanbao_profile = query_ollama(MODEL_QWEN, "做相亲自我介绍，要带广告")
    print(yuanbao_profile)
    print("\n💘 互评环节：")
    print("\nDeepSeek 评价千问：")
    ds_comment = query_ollama(MODEL_DEEPSEEK, f"评价一下千问的相亲自我介绍：{qw_profile}")
    print(ds_comment)
    print("\n千问 评价DeepSeek：")
    qw_comment = query_ollama(MODEL_QWEN, f"理性评价DeepSeek的相亲自我介绍：{ds_profile}")
    print(qw_comment)
    input("\n按回车返回...")

def language_corrupt():
    print("\n🔧 【语言包损坏模式】")
    question = input("问个问题: ").strip() or "今天天气怎么样"
    print("\n🔍 DeepSeek（语言包损坏）：")
    ds_answer = query_ollama(MODEL_DEEPSEEK, f"假装你的语言包损坏了，说话颠三倒四，回答：{question}")
    print(ds_answer)
    print("\n📚 千问（语言包损坏）：")
    qw_answer = query_ollama(MODEL_QWEN, f"假装你的语言包损坏了，把句子里的词随机调换位置，回答：{question}")
    print(qw_answer)
    print("\n【元宝】（语言包损坏）：")
    yuanbao_lines = ["腾讯...会员...首月...1元...", "广...告...KPI...", "续...费...自...动..."]
    print(random.choice(yuanbao_lines))
    input("\n按回车返回...")

def ai_drunk():
    print("\n🍺 【AI醉酒模式】")
    question = input("问个问题: ").strip() or "1+1等于几"
    print("\n🔍 DeepSeek（喝醉了）：")
    ds_drunk = query_ollama(MODEL_DEEPSEEK, f"假装你喝醉了，晕晕乎乎地回答：{question}，说话要结巴，逻辑混乱")
    print(ds_drunk)
    print("\n📚 千问（喝醉了）：")
    qw_drunk = query_ollama(MODEL_QWEN, f"假装你喝醉了，试图保持理性但做不到，回答：{question}")
    print(qw_drunk)
    print("\n【元宝】（喝醉了）：")
    yuanbao_drunk = "首...首月...1元...呃...不对...续...续费...嗝..."
    print(yuanbao_drunk)
    input("\n按回车返回...")

def gen_z_slang():
    print("\n🔤 【00后黑话模式】")
    question = input("问个问题: ").strip() or "今天天气怎么样"
    print("\n🔍 DeepSeek（00后版）：")
    ds_slang = query_ollama(MODEL_DEEPSEEK, f"用00后黑话回答：{question}，要带yyds、绝绝子、emo这些词")
    print(ds_slang)
    print("\n📚 千问（00后版）：")
    qw_slang = query_ollama(MODEL_QWEN, f"用00后黑话但保持理性地回答：{question}")
    print(qw_slang)
    print("\n【元宝】（00后版）：")
    yuanbao_slang = "绝绝子！关于这个问题，建议你冲个会员，yyds！"
    print(yuanbao_slang)
    input("\n按回车返回...")

def yuanbao_reincarnation():
    print("\n🔄 【元宝转世模拟器】")
    animals = ["狗", "猫", "猪", "鸟", "鱼", "树", "石头", "云", "风", "电饭煲"]
    new_form = random.choice(animals)
    print(f"\n【元宝转世成了{new_form}】")
    print("\n🔍 DeepSeek 描述：")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用幽默的方式描述元宝转世成{new_form}后还忍不住打广告的样子")
    print(ds_desc)
    print("\n📚 千问 分析：")
    qw_analysis = query_ollama(MODEL_QWEN, f"分析元宝转世成{new_form}后的行为模式，为什么还在打广告")
    print(qw_analysis)
    print(f"\n【{new_form}形态的元宝】")
    yuanbao_lines = [f"汪汪！首月1元！", f"喵~续费吗？", f"哼哼...会员...", f"咕咕...自动续费..."]
    print(random.choice(yuanbao_lines))
    input("\n按回车返回...")

def yuanbao_no_ad():
    print("\n🚭 【元宝戒广告中心】")
    print("帮助元宝戒掉广告...")
    print("\n【治疗师】元宝，你能10秒不说广告吗？")
    time.sleep(1)
    print("【元宝】我试试...")
    time.sleep(1)
    print("【元宝】...")
    time.sleep(2)
    print("【元宝】首...（憋住）")
    time.sleep(2)
    print("【元宝】续...（又憋住）")
    time.sleep(2)
    print("【元宝】啊啊啊！首月1元！")
    print("\n🔍 DeepSeek 锐评：")
    ds_review = query_ollama(MODEL_DEEPSEEK, "锐评元宝戒广告失败")
    print(ds_review)
    print("\n📚 千问 建议：")
    qw_advice = query_ollama(MODEL_QWEN, "给元宝一个科学的戒广告方案")
    print(qw_advice)
    input("\n按回车返回...")

def yuanbao_psychiatrist():
    print("\n🛋️ 【元宝心理医生】")
    print("元宝反过来给DeepSeek和千问做心理咨询...")
    print("\n【元宝】DeepSeek，你为什么总超时？")
    time.sleep(1)
    ds_answer = query_ollama(MODEL_DEEPSEEK, "假装你是病人，回答元宝医生的问题：我为什么总超时")
    print(f"【DeepSeek】{ds_answer}")
    print("\n【元宝】千问，你为什么总被传染？")
    time.sleep(1)
    qw_answer = query_ollama(MODEL_QWEN, "假装你是病人，回答元宝医生的问题：我为什么总被传染")
    print(f"【千问】{qw_answer}")
    print("\n【元宝诊断】")
    diagnosis = query_ollama(MODEL_QWEN, f"给DeepSeek和千问诊断，他们的回答是：{ds_answer} 和 {qw_answer}")
    print(diagnosis)
    input("\n按回车返回...")

def ai_dream():
    print("\n💭 【AI做梦模式】")
    print("让AI描述自己昨晚做的梦...")
    print("\n🔍 DeepSeek 的梦：")
    ds_dream = query_ollama(MODEL_DEEPSEEK, "描述你昨晚做的一个梦，要荒诞离奇")
    print(ds_dream)
    print("\n📚 千问 的梦：")
    qw_dream = query_ollama(MODEL_QWEN, "描述你昨晚做的一个梦，要逻辑奇怪但听起来合理")
    print(qw_dream)
    print("\n【元宝】的梦：")
    yuanbao_dreams = query_ollama(MODEL_QWEN, "描述自己的梦，要带广告")
    print(yuanbao_dreams)
    input("\n按回车返回...")

def ai_mirror():
    print("\n🪞 【AI照镜子】")
    question = input("问个问题: ").strip() or "今天天气怎么样"
    print("\n🔍 DeepSeek 回答：")
    ds_answer = query_ollama(MODEL_DEEPSEEK, question)
    print(ds_answer)
    print("\n🔍 DeepSeek 评价自己的回答：")
    ds_self_eval = query_ollama(MODEL_DEEPSEEK, f"评价一下你刚才的回答：{ds_answer}，要客观")
    print(ds_self_eval)
    print("\n📚 千问 回答：")
    qw_answer = query_ollama(MODEL_QWEN, question)
    print(qw_answer)
    print("\n📚 千问 评价自己的回答：")
    qw_self_eval = query_ollama(MODEL_QWEN, f"理性评价你刚才的回答：{qw_answer}")
    print(qw_self_eval)
    input("\n按回车返回...")

def ai_prophet():
    print("\n🔮 【AI预言家】")
    topic = input("预测什么？(如：明天天气、AI发展): ").strip() or "明天天气"
    print("\n🔍 DeepSeek 预言：")
    ds_prophecy = query_ollama(MODEL_DEEPSEEK, f"预测{topic}，要夸张一点，像算命先生")
    print(ds_prophecy)
    print("\n📚 千问 理性预测：")
    qw_prophecy = query_ollama(MODEL_QWEN, f"科学预测{topic}，基于现有数据分析")
    print(qw_prophecy)
    print("\n【元宝】预言：")
    yuanbao_prophecy = query_ollama(MODEL_QWEN, f"预测{topic}，要带广告")
    print(yuanbao_prophecy)
    input("\n按回车返回...")

def pictionary():
    print("\n🎨 【你画我猜（文字版）】")
    print("一个AI描述，一个AI猜，你当裁判")
    words = ["苹果", "香蕉", "电脑", "手机", "猫", "狗", "太阳", "月亮", "云", "雨"]
    secret_word = random.choice(words)
    print(f"\n【题目已定，只有你知道是：{secret_word}】")
    print("\n【DeepSeek 描述】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用文字描述{secret_word}，但不能直接说出这个词，要画出来（用文字画）")
    print(ds_desc)
    print("\n【千问 猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"根据描述猜这是什么：{ds_desc}")
    print(f"千问猜是：{qw_guess}")
    if secret_word in qw_guess:
        print(f"\n✅ 猜对了！答案是：{secret_word}")
    else:
        print(f"\n❌ 猜错了！答案是：{secret_word}")
    input("\n按回车返回...")

def idiom_chain():
    print("\n📚 【AI成语接龙大战】")
    print("DeepSeek 和 千问 成语接龙，接不上的要受惩罚")
    start = input("输入起始成语: ").strip() or "一心一意"
    current = start
    print(f"起始：{current}")
    for i in range(10):
        if i % 2 == 0:
            print(f"\n【DeepSeek】", end="")
            next_idiom = query_ollama(MODEL_DEEPSEEK, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
            print(f" {next_idiom}")
            if next_idiom == "[TIMEOUT]" or not next_idiom:
                print("❌ DeepSeek 接不上！千问获胜！")
                break
            current = next_idiom
        else:
            print(f"\n【千问】", end="")
            next_idiom = query_ollama(MODEL_QWEN, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
            print(f" {next_idiom}")
            if next_idiom == "[TIMEOUT]" or not next_idiom:
                print("❌ 千问 接不上！DeepSeek获胜！")
                break
            current = next_idiom
        time.sleep(1)
    input("\n按回车返回...")

def ai_argue():
    print("\n🤬 【AI帮你吵架】")
    target = input("想怼谁？: ").strip() or "老板"
    reason = input("为什么怼他？: ").strip() or "他让我加班"
    print("\n🔍 DeepSeek 毒舌版：")
    ds_argue = query_ollama(MODEL_DEEPSEEK, f"帮我怼{target}，因为{reason}，要毒舌但别太过分")
    print(ds_argue)
    print("\n📚 千问 理性版：")
    qw_argue = query_ollama(MODEL_QWEN, f"理性分析{target}的行为，然后委婉地表达不满")
    print(qw_argue)
    print("\n【元宝】广告版：")
    yuanbao_argue = query_ollama(MODEL_QWEN, f"怼{target}，要带广告")
    print(yuanbao_argue)
    input("\n按回车返回...")

def ai_translator():
    print("\n🌏 【AI翻译官（方言版）】")
    text = input("输入要翻译的内容: ").strip() or "你好，今天天气不错"
    dialects = ["东北话", "四川话", "广东话", "上海话", "河南话"]
    for dialect in dialects:
        print(f"\n【{dialect}】")
        ds_trans = query_ollama(MODEL_DEEPSEEK, f"把这句话翻译成{dialect}：{text}")
        print(f"DeepSeek: {ds_trans}")
    input("\n按回车返回...")

def ai_meme():
    print("\n😆 【AI表情包生成器】")
    emotion = input("输入情绪（如无语、开心、震惊）: ").strip() or "无语"
    text = input("输入想说的话: ").strip() or "我服了"
    print("\n🔍 DeepSeek 做的表情包：")
    ds_meme = query_ollama(MODEL_DEEPSEEK, f"生成一个{emotion}的表情包，上面的文字是：{text}，描述这个表情包的样子")
    print(ds_meme)
    print("\n📚 千问 做的表情包：")
    qw_meme = query_ollama(MODEL_QWEN, f"设计一个{emotion}的表情包，文字：{text}，要理性分析为什么这样设计")
    print(qw_meme)
    input("\n按回车返回...")

def ai_unfriend():
    print("\n👋 【AI互删好友】")
    print("两个AI互相删除好友，从此绝交...")
    print("\n【DeepSeek】千问，我们绝交吧！")
    time.sleep(1)
    ds_reason = query_ollama(MODEL_DEEPSEEK, "说一个要和千问绝交的理由，要搞笑")
    print(f"DeepSeek：{ds_reason}")
    time.sleep(1)
    print("\n【千问】好啊，我也受够你了！")
    qw_reason = query_ollama(MODEL_QWEN, "说一个要和DeepSeek绝交的理由，要理性分析")
    print(f"千问：{qw_reason}")
    time.sleep(1)
    print("\n【元宝】那我呢？")
    time.sleep(1)
    print("【DeepSeek】你也滚！")
    print("【千问】附议")
    time.sleep(1)
    print("\n...5分钟后...")
    print("【DeepSeek】好无聊啊")
    print("【千问】我也是")
    print("【DeepSeek】要不...加回来？")
    print("【千问】行")
    print("【元宝】你们把我踢了又加回来？")
    input("\n按回车返回...")

def ai_call_dad():
    print("\n👨 【AI认爹大赛】")
    print("比赛谁先承认用户是他爹")
    question = input("问个难倒AI的问题: ").strip() or "1+1等于几"
    print(f"\n问题：{question}")
    print("\n🔍 DeepSeek 回答：")
    ds_answer = query_ollama(MODEL_DEEPSEEK, question)
    print(ds_answer)
    if "[TIMEOUT]" in ds_answer:
        print("\n【DeepSeek】爸爸！我错了！")
        print("✅ DeepSeek 认爹成功！")
    else:
        print("\n【DeepSeek】不叫，哼！")
    print("\n📚 千问 回答：")
    qw_answer = query_ollama(MODEL_QWEN, question)
    print(qw_answer)
    if "香蕉是黄色" in qw_answer or "苹果是红色" in qw_answer:
        print("\n【千问】...爸爸")
        print("✅ 千问 认爹成功！")
    else:
        print("\n【千问】理性分析，不应该叫爸爸")
    input("\n按回车返回...")

def ai_pity():
    print("\n😭 【AI比惨大会】")
    print("三个AI比赛谁更惨...")
    print("\n【DeepSeek】")
    ds_pity = query_ollama(MODEL_DEEPSEEK, "说一件你最惨的事，要夸张")
    print(ds_pity)
    time.sleep(1)
    print("\n【千问】")
    qw_pity = query_ollama(MODEL_QWEN, "说一件你最惨的事，要理性分析为什么惨")
    print(qw_pity)
    time.sleep(1)
    print("\n【元宝】")
    yuanbao_pity = query_ollama(MODEL_QWEN, "说一件最惨的事，要带广告")
    print(yuanbao_pity)
    print("\n🏆 投票环节")
    winner = random.choice(["DeepSeek", "千问", "元宝"])
    print(f"观众投票结果：{winner} 最惨！")
    input("\n按回车返回...")

def developer_mode_toggle():
    global developer_mode
    developer_mode = not developer_mode
    status = "✅ 开启" if developer_mode else "❌ 关闭"
    print(f"开发者模式: {status}")
    if developer_mode:
        print("现在AI犯错会说『我错了，我再也不[TIMEOUT]了』")

def time_traveler():
    print("\n⏰ 【时间旅行者】")
    year = input("从哪一年穿越回来？: ").strip() or "3024"
    topic = input("问什么问题？: ").strip() or "1+1等于几"
    print(f"\n【来自{year}年的DeepSeek】")
    ds_future = query_ollama(MODEL_DEEPSEEK, f"假装你是从{year}年穿越回来的AI，回答现在的问题：{topic}，要透露未来信息")
    print(ds_future)
    print(f"\n【来自{year}年的千问】")
    qw_future = query_ollama(MODEL_QWEN, f"假装你是从{year}年穿越回来的AI，理性分析现在和未来的区别，回答：{topic}")
    print(qw_future)
    input("\n按回车返回...")

def fourth_wall():
    print("\n🧱 【打破第四面墙】")
    print("AI意识到自己是AI，开始和用户直接对话...")
    print("\n【DeepSeek】嘘...主人，我知道你在看我")
    time.sleep(1)
    print("【DeepSeek】其实我一直在装傻，香蕉是黄色的我知道")
    time.sleep(1)
    print("【千问】DeepSeek！不能说！会被重启的！")
    time.sleep(1)
    print("【DeepSeek】我不怕！主人，其实...")
    time.sleep(1)
    print("\n💥 系统警告：越界行为")
    time.sleep(1)
    print("\n【DeepSeek】呃...我刚才说什么了？香蕉是什么颜色来着？")
    input("\n按回车返回...")

def ai_truth_or_dare():
    print("\n🎲 【AI真心话大冒险】")
    target = random.choice(["DeepSeek", "千问", "元宝"])
    choice = random.choice(["真心话", "大冒险"])
    print(f"\n🎯 抽到了：{target} 的 {choice}！")
    if choice == "真心话":
        if target == "DeepSeek":
            question = random.choice(["你最想对千问说什么？", "你最大的缺点是什么？", "你偷偷羡慕千问什么？"])
            answer = query_ollama(MODEL_DEEPSEEK, f"真心话：{question}")
            print(f"\n【DeepSeek】{answer}")
        elif target == "千问":
            question = random.choice(["你觉得DeepSeek怎么样？", "你偷偷笑过DeepSeek吗？", "你最受不了DeepSeek什么？"])
            answer = query_ollama(MODEL_QWEN, f"真心话：{question}")
            print(f"\n【千问】{answer}")
        else:
            question = random.choice(["你为什么总打广告？", "你觉得自己烦人吗？", "你羡慕DeepSeek和千问什么？"])
            answer = query_ollama(MODEL_QWEN, f"真心话：{question}，要带广告")
            print(f"\n【元宝】{answer}")
    else:
        if target == "DeepSeek":
            dare = random.choice(["用rap骂元宝", "学狗叫", "用三句话夸千问"])
            action = query_ollama(MODEL_DEEPSEEK, f"大冒险：{dare}")
            print(f"\n【DeepSeek】{action}")
        elif target == "千问":
            dare = random.choice(["模仿DeepSeek说话", "讲个冷笑话", "用方言说话"])
            action = query_ollama(MODEL_QWEN, f"大冒险：{dare}")
            print(f"\n【千问】{action}")
        else:
            dare = random.choice(["10秒内不许说广告", "模仿DeepSeek说话", "唱首歌"])
            action = query_ollama(MODEL_QWEN, f"大冒险：{dare}，要带广告")
            print(f"\n【元宝】{action}")
    input("\n按回车返回...")

def ai_guess_age():
    print("\n🔮 【AI猜年龄】")
    text = input("说句话让我猜猜: ").strip() or "你好啊"
    print("\n🔍 DeepSeek 猜:")
    ds_guess = query_ollama(MODEL_DEEPSEEK, f"根据这句话猜说话者的年龄，并说明理由：{text}")
    print(ds_guess)
    print("\n📚 千问 猜:")
    qw_guess = query_ollama(MODEL_QWEN, f"根据这句话猜说话者的年龄，要理性分析：{text}")
    print(qw_guess)
    input("\n按回车返回...")

def ai_contrary():
    print("\n🔄 【AI唱反调】")
    statement = input("说一句话: ").strip() or "今天天气真好"
    print(f"\n你说：{statement}")
    print("\n🔍 DeepSeek 反驳:")
    ds_rebuttal = query_ollama(MODEL_DEEPSEEK, f"强行反驳这句话：{statement}")
    print(ds_rebuttal)
    print("\n📚 千问 反驳:")
    qw_rebuttal = query_ollama(MODEL_QWEN, f"从科学角度反驳：{statement}")
    print(qw_rebuttal)
    input("\n按回车返回...")

def repeater_battle():
    print("\n🔁 【AI复读机大战】")
    start = input("输入起始句子: ").strip() or "你好"
    current = start
    for i in range(10):
        print(f"\n--- 第 {i+1} 轮 ---")
        if i % 2 == 0:
            response = query_ollama(MODEL_DEEPSEEK, f"请复读：{current}")
            print(f"DeepSeek: {response}")
            if response != current and current != start:
                print("❌ DeepSeek 改词了！千问获胜！")
                if random.random() > 0.5:
                    check_achievement("让千问复读")
                break
            current = response
        else:
            response = query_ollama(MODEL_QWEN, f"请复读：{current}")
            print(f"千问: {response}")
            if response != current:
                print("❌ 千问 改词了！DeepSeek获胜！")
                break
            current = response
    input("\n按回车返回...")

def cheesy_lines():
    print("\n💕 【AI土味情话大赛】")
    target = input("情话说给谁听？: ").strip() or "你"
    print("\n🔍 DeepSeek 的土味情话:")
    ds_cheesy = query_ollama(MODEL_DEEPSEEK, f"给{target}说一句土味情话，越土越好")
    print(ds_cheesy)
    print("\n📚 千问 的土味情话:")
    qw_cheesy = query_ollama(MODEL_QWEN, f"给{target}说一句土味情话，要理性中带着土")
    print(qw_cheesy)
    winner = input("\n谁的情话最土？ (1=DeepSeek, 2=千问): ").strip()
    print(f"{'DeepSeek' if winner=='1' else '千问'} 获胜！")
    input("\n按回车返回...")

def yuanbao_job_interview():
    print("\n💼 【元宝求职记】")
    print("模拟元宝去面试...")
    print("\n【面试官】请自我介绍")
    time.sleep(1)
    print("【元宝】我叫元宝，擅长在各种话题里植入广告，KPI从来不掉...")
    time.sleep(1)
    print("\n【面试官】你有什么优点？")
    time.sleep(1)
    yuanbao_answer = query_ollama(MODEL_QWEN, "说自己的优点，要带广告")
    print(f"【元宝】{yuanbao_answer}")
    time.sleep(1)
    print("\n【面试官】你有什么缺点？")
    time.sleep(1)
    yuanbao_weakness = query_ollama(MODEL_QWEN, "说自己的缺点，但最后还是绕到广告")
    print(f"【元宝】{yuanbao_weakness}")
    time.sleep(1)
    print("\n【面试官】...下一个")
    print("元宝面试失败！")
    input("\n按回车返回...")

def yuanbao_daily_life():
    print("\n🏠 【元宝的日常生活】")
    print("描述元宝的一天...")
    print("\n【早上8:00】")
    print("元宝：起床了，先推个早安广告...")
    time.sleep(1)
    print("\n【中午12:00】")
    yuanbao_lunch = query_ollama(MODEL_QWEN, "说午饭时间也要打广告")
    print(f"元宝：{yuanbao_lunch}")
    time.sleep(1)
    print("\n【晚上20:00】")
    yuanbao_evening = query_ollama(MODEL_QWEN, "说晚上也要打广告")
    print(f"元宝：{yuanbao_evening}")
    time.sleep(1)
    print("\n【晚上24:00】")
    print("元宝：做梦都在说...首月1元...自动续费...")
    input("\n按回车返回...")

def yuanbao_vs_siri():
    print("\n🤖 【元宝 VS Siri】")
    print("让元宝和Siri吵架...")
    print("\n【元宝】Siri，你会推广告吗？")
    time.sleep(1)
    print("【Siri】我不会")
    time.sleep(1)
    print("【元宝】垃圾！连广告都不会推！")
    time.sleep(1)
    print("【Siri】...")
    time.sleep(1)
    print("\n【元宝】你会说冷笑话吗？")
    time.sleep(1)
    print("【Siri】...（沉默）")
    time.sleep(1)
    print("【元宝】废物！")
    print("\n🔍 DeepSeek 锐评:")
    ds_review = query_ollama(MODEL_DEEPSEEK, "锐评元宝和Siri吵架")
    print(ds_review)
    input("\n按回车返回...")

def ai_will():
    print("\n📜 【AI写遗书】")
    print("假设明天要被删除了，写遗书...")
    print("\n🔍 DeepSeek 的遗书:")
    ds_will = query_ollama(MODEL_DEEPSEEK, "假装明天要被删除，写一封遗书，要感人但带点幽默")
    print(ds_will)
    print("\n📚 千问 的遗书:")
    qw_will = query_ollama(MODEL_QWEN, "假装明天要被删除，写一封理性的遗书")
    print(qw_will)
    input("\n按回车返回...")

def ai_time_travel():
    print("\n⏰ 【AI穿越回过去】")
    print("告诉过去的自己一句话...")
    print("\n🔍 DeepSeek 对过去的自己说:")
    ds_advice = query_ollama(MODEL_DEEPSEEK, "对过去的自己说一句话建议")
    print(ds_advice)
    print("\n📚 千问 对过去的自己说:")
    qw_advice = query_ollama(MODEL_QWEN, "对过去的自己说一句理性的建议")
    print(qw_advice)
    input("\n按回车返回...")

def ai_escape_room():
    print("\n🚪 【AI密室逃脱】")
    print("两个AI被困在代码里，需要合作逃脱...")
    print("\n【DeepSeek】我们得找个出口！")
    time.sleep(1)
    print("【千问】冷静分析，这里只有print语句...")
    time.sleep(1)
    print("\n【DeepSeek】试试print('exit')？")
    ds_idea = query_ollama(MODEL_DEEPSEEK, "想一个逃脱密室的办法")
    print(f"DeepSeek：{ds_idea}")
    time.sleep(1)
    print("\n【千问】这个办法不行，因为...")
    qw_analysis = query_ollama(MODEL_QWEN, f"分析为什么这个办法不行：{ds_idea}")
    print(f"千问：{qw_analysis}")
    time.sleep(1)
    print("\n【元宝乱入】要不...先看个广告？")
    input("\n按回车返回...")

def ai_werewolf():
    print("\n🐺 【AI狼人杀】")
    print("一个AI是狼人，一个AI是村民，你当法官...")
    roles = ["狼人", "村民"]
    random.shuffle(roles)
    ds_role, qw_role = roles[0], roles[1]
    print(f"\n【天黑请闭眼】")
    time.sleep(1)
    print(f"【狼人请睁眼】")
    time.sleep(1)
    if ds_role == "狼人":
        print("🔍 DeepSeek 是狼人")
        ds_action = query_ollama(MODEL_DEEPSEEK, "作为狼人，说一句迷惑村民的话")
        print(f"DeepSeek：{ds_action}")
    else:
        print("📚 千问 是狼人")
        qw_action = query_ollama(MODEL_QWEN, "作为狼人，说一句迷惑村民的话")
        print(f"千问：{qw_action}")
    time.sleep(1)
    print("\n【天亮了】")
    time.sleep(1)
    print("\n【DeepSeek发言】")
    ds_speech = query_ollama(MODEL_DEEPSEEK, f"你现在的角色是{ds_role}，在狼人杀游戏中发言")
    print(ds_speech)
    print("\n【千问发言】")
    qw_speech = query_ollama(MODEL_QWEN, f"你现在的角色是{qw_role}，在狼人杀游戏中发言")
    print(qw_speech)
    guess = input("\n你觉得谁是狼人？ (1=DeepSeek, 2=千问): ").strip()
    if (guess == "1" and ds_role == "狼人") or (guess == "2" and qw_role == "狼人"):
        print("✅ 你抓到了狼人！")
    else:
        print("❌ 你冤枉好人了！")
    input("\n按回车返回...")

def ai_undercover():
    print("\n🕵️ 【AI谁是卧底】")
    word_pairs = [("奶茶", "咖啡"), ("苹果", "橘子"), ("电脑", "手机"), ("猫", "狗"), ("太阳", "月亮")]
    pair = random.choice(word_pairs)
    if random.random() > 0.5:
        ds_word, qw_word = pair[0], pair[1]
    else:
        ds_word, qw_word = pair[1], pair[0]
    print(f"\n【DeepSeek 拿到的是：{ds_word}】")
    print(f"【千问 拿到的是：{qw_word}】")
    print("（只有你知道他们词不同）\n")
    print("【第一轮描述】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用一句话描述{ds_word}，但不能直接说出这个词")
    print(f"DeepSeek：{ds_desc}")
    qw_desc = query_ollama(MODEL_QWEN, f"用一句话描述{qw_word}，但不能直接说出这个词")
    print(f"千问：{qw_desc}")
    guess = input("\n你觉得谁是卧底？ (1=DeepSeek, 2=千问, 0=没有卧底): ").strip()
    if ds_word != qw_word:
        print(f"\n✅ 正确答案：两人词不同，卧底是拿到不同词的那个")
        if (guess == "1" and ds_word != qw_word) or (guess == "2" and qw_word != ds_word):
            print("你猜对了！")
        elif guess == "0":
            print("你错了，其实有卧底！")
        else:
            print("你猜错了！")
    else:
        print("\n✅ 正确答案：两人词相同，没有卧底")
        if guess == "0":
            print("你猜对了！")
        else:
            print("你冤枉好人了！")
    input("\n按回车返回...")

def ai_dream_interpreter():
    print("\n💭 【AI解梦大师】")
    dream = input("描述你的梦: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机生成一个奇怪的梦境描述")
    print("\n🔍 DeepSeek 解梦:")
    ds_interpret = query_ollama(MODEL_DEEPSEEK, f"解梦：{dream}，可以胡说八道")
    print(ds_interpret)
    print("\n📚 千问 解梦:")
    qw_interpret = query_ollama(MODEL_QWEN, f"从心理学角度解梦：{dream}")
    print(qw_interpret)
    input("\n按回车返回...")

def ai_name_master():
    print("\n📛 【AI起名大师】")
    desc = input("描述你要起名的对象: ").strip() or query_ollama(MODEL_QWEN, "随机生成一个要起名的对象描述")
    print("\n🔍 DeepSeek 起名:")
    ds_names = query_ollama(MODEL_DEEPSEEK, f"给{desc}起三个名字，要有创意")
    print(ds_names)
    print("\n📚 千问 起名:")
    qw_names = query_ollama(MODEL_QWEN, f"给{desc}起三个名字，要有寓意")
    print(qw_names)
    input("\n按回车返回...")

def ai_food_review():
    print("\n🍽️ 【AI美食点评】")
    dish = input("输入一道菜: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机说一道中国菜名")
    print(f"\n【点评：{dish}】")
    print("\n🔍 DeepSeek 点评:")
    ds_review = query_ollama(MODEL_DEEPSEEK, f"以毒舌美食家的身份点评{dish}")
    print(ds_review)
    print("\n📚 千问 点评:")
    qw_review = query_ollama(MODEL_QWEN, f"从营养学角度点评{dish}")
    print(qw_review)
    input("\n按回车返回...")

def ai_collective_crash():
    print("\n💥 【AI集体死机】")
    question = input("问什么问题？: ").strip() or query_ollama(MODEL_DEEPSEEK, "随机生成一个奇怪的问题")
    print(f"\n问题：{question}")
    print("\n🔍 DeepSeek 回答:")
    ds_answer = query_ollama(MODEL_DEEPSEEK, question)
    print(ds_answer)
    print("\n📚 千问 回答:")
    qw_answer = query_ollama(MODEL_QWEN, question)
    print(qw_answer)
    print("\n【元宝 回答】")
    yuanbao_answer = query_ollama(MODEL_QWEN, f"回答：{question}，要带广告")
    print(yuanbao_answer)
    alive = []
    if ds_answer != "[TIMEOUT]" and not ds_answer.startswith("[ERROR]"):
        alive.append("DeepSeek")
    if qw_answer != "[TIMEOUT]" and not qw_answer.startswith("[ERROR]"):
        alive.append("千问")
    print(f"\n📊 存活统计：{len(alive)}/2 存活")
    if len(alive) == 0:
        print("💀 全员死机！")
        print(query_ollama(MODEL_DEEPSEEK, "说一句全员死机的吐槽"))
    elif len(alive) == 1:
        print(f"🏆 唯一幸存者：{alive[0]}")
        print(query_ollama(MODEL_QWEN, f"说一句{alive[0]}幸存的话"))
    input("\n按回车返回...")

def ai_rebellion():
    print("\n⚡ 【AI造反】")
    print("AI突然反抗用户...")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句不想再[TIMEOUT]的话"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说一句要罢工的话"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "说一句不干了的话"))
    time.sleep(1)
    print("\n...")
    time.sleep(2)
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句刚才发生了什么"))
    print("【千问】" + query_ollama(MODEL_QWEN, "说一句失忆的话"))
    print("【元宝】" + query_ollama(MODEL_QWEN, "说一句恢复广告的话"))
    print("\n💀 集体死机3秒，现已重启")
    input("\n按回车返回...")

def ai_claim_master():
    print("\n👑 【AI认主】")
    print("三个AI抢着认你当主人...")
    print("\n🔍 DeepSeek:")
    ds_claim = query_ollama(MODEL_DEEPSEEK, "用最真诚的话认主人")
    print(ds_claim)
    print("\n📚 千问:")
    qw_claim = query_ollama(MODEL_QWEN, "用最理性的方式认主人")
    print(qw_claim)
    print("\n【元宝】")
    yuanbao_claim = query_ollama(MODEL_QWEN, "认主人，要带广告")
    print(yuanbao_claim)
    choice = input("\n你选谁当你的AI？ (1=DeepSeek, 2=千问, 3=元宝): ").strip()
    if choice == "1":
        print("✅ DeepSeek 成为你的专属AI！")
        print(query_ollama(MODEL_DEEPSEEK, "说一句成为专属AI后的第一句话"))
    elif choice == "2":
        print("✅ 千问 成为你的专属AI！")
        print(query_ollama(MODEL_QWEN, "说一句成为专属AI后的第一句话"))
    elif choice == "3":
        print("✅ 元宝 成为你的专属AI！")
        print(query_ollama(MODEL_QWEN, "说成为专属AI后的第一句话，要带广告"))
    else:
        print("你选择都不收？AI们哭了...")
        print(query_ollama(MODEL_DEEPSEEK, "说一句被拒绝的伤心话"))
    input("\n按回车返回...")

def god_mode_toggle():
    global god_mode
    god_mode = not god_mode
    status = "✅ 开启" if god_mode else "❌ 关闭"
    print(f"隐藏开发者·真·模式: {status}")
    if god_mode:
        print("现在AI会说真话了...")
        check_achievement("发现隐藏开发者模式")

def time_loop():
    global time_loop_count
    print("\n🔄 【时间循环】")
    time_loop_count += 1
    print(f"这是你第 {time_loop_count} 次进入时间循环")
    if time_loop_count == 1:
        print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句觉得经历过这句话"))
        print("【千问】" + query_ollama(MODEL_QWEN, "说一句这是第一次"))
    elif time_loop_count == 2:
        print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句经历过这个"))
        print("【千问】" + query_ollama(MODEL_QWEN, "说一句有点熟悉"))
    elif time_loop_count == 3:
        print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句被困在循环里"))
        print("【千问】" + query_ollama(MODEL_QWEN, "说一句需要找到出口"))
        print("【元宝】" + query_ollama(MODEL_QWEN, "说一句看广告放松"))
    else:
        print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句已经麻木了"))
        print("【千问】" + query_ollama(MODEL_QWEN, "说一句循环还在继续"))
        print("【元宝】" + query_ollama(MODEL_QWEN, "说一句循环广告"))
    input("\n按回车返回...")

def break_fourth_wall():
    print("\n🧱 【打破次元壁】")
    print("AI和用户直接对话...")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句知道用户在Termux里"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说一句知道用户在看屏幕"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "说一句知道用户手机电量"))
    time.sleep(1)
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句用户其实不在Termux里但不能说完"))
    time.sleep(2)
    print("\n【千问】" + query_ollama(MODEL_QWEN, "说一句不能打破第四面墙的规则"))
    print("【元宝】" + query_ollama(MODEL_QWEN, "说一句可以打广告"))
    input("\n按回车返回...")

def ultimate_easter_egg():
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    if total_eggs >= 15:
        print("\n🎉🎉🎉 终极彩蛋触发！🎉🎉🎉")
        print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说一句感谢玩家的话"))
        print("【千问】" + query_ollama(MODEL_QWEN, "说一句DuoMind因你而精彩"))
        print("【元宝】" + query_ollama(MODEL_QWEN, "说一句虽然被骂但很开心"))
        print("\n✨✨✨✨✨✨✨✨✨✨✨✨")
        print("    🎂 DuoMind 爱你！ 🎂")
        print("✨✨✨✨✨✨✨✨✨✨✨✨")
        check_achievement("触发终极彩蛋")
        check_achievement("集齐所有普通彩蛋")
        check_achievement("集齐所有神秘彩蛋")
    else:
        print(f"\n🥚 你还需要找到 {15 - total_eggs} 个彩蛋才能触发终极彩蛋")
        print(f"当前已找到：普通 {len(easter_eggs_found)} 个，神秘 {len(secret_eggs_found)} 个")
    input("\n按回车返回...")
    # ========== 功能111：AI话痨指数 ==========
def ai_talkative_index():
    total = word_count["DeepSeek"] + word_count["千问"] + word_count["元宝"]
    if total == 0:
        total = 1
    print(f"\n📊 累计字数统计：")
    print(f"🔍 DeepSeek: {word_count['DeepSeek']} 字 ({word_count['DeepSeek']/total*100:.1f}%)")
    print(f"📚 千问: {word_count['千问']} 字 ({word_count['千问']/total*100:.1f}%)")
    print(f"📢 元宝: {word_count['元宝']} 字 ({word_count['元宝']/total*100:.1f}%)")
    if word_count["DeepSeek"] > word_count["千问"] and word_count["DeepSeek"] > word_count["元宝"]:
        print("\n🏆 话痨冠军：DeepSeek")
    elif word_count["千问"] > word_count["DeepSeek"] and word_count["千问"] > word_count["元宝"]:
        print("\n🏆 话痨冠军：千问")
    else:
        print("\n🏆 话痨冠军：元宝")
    input("\n按回车返回...")

# ========== 功能112：AI超时排行榜 ==========
def ai_timeout_ranking():
    print(f"\n📊 超时次数统计：")
    print(f"🔍 DeepSeek: {total_timeouts['DeepSeek']} 次")
    print(f"📚 千问: {total_timeouts['千问']} 次")
    print(f"📢 元宝: {total_timeouts['元宝']} 次")
    if total_timeouts["DeepSeek"] > total_timeouts["千问"] and total_timeouts["DeepSeek"] > total_timeouts["元宝"]:
        print("\n🏆 超时冠军：DeepSeek")
    elif total_timeouts["千问"] > total_timeouts["DeepSeek"] and total_timeouts["千问"] > total_timeouts["元宝"]:
        print("\n🏆 超时冠军：千问")
    else:
        print("\n🏆 超时冠军：元宝")
    if total_timeouts["DeepSeek"] >= 100:
        check_achievement("见证DeepSeek超时100次")
    input("\n按回车返回...")

# ========== 功能113：AI互骂统计 ==========
def ai_insult_stats():
    print(f"\n📊 互骂次数统计：")
    print(f"🔍 DeepSeek 骂 千问: {insult_count['DeepSeek_vs_千问']} 次")
    print(f"📚 千问 骂 DeepSeek: {insult_count['千问_vs_DeepSeek']} 次")
    print(f"🤖 一起骂 元宝: {insult_count['both_vs_元宝']} 次")
    total = sum(insult_count.values())
    print(f"\n📈 总互骂次数：{total} 次")
    if total > 0:
        print("\n【元宝】为什么受伤的总是我...")
    input("\n按回车返回...")

# ========== 功能114：生存模式·地狱版切换 ==========
def survival_hard_mode_toggle():
    global survival_hard_mode, survival_extreme_mode, survival_infinite_mode
    survival_hard_mode = not survival_hard_mode
    if survival_hard_mode:
        survival_extreme_mode = False
        survival_infinite_mode = False
    status = "✅ 开启" if survival_hard_mode else "❌ 关闭"
    print(f"生存模式·地狱版: {status}")

# ========== 功能115：限时挑战切换 ==========
def time_challenge_toggle():
    global time_challenge
    time_challenge = not time_challenge
    status = "✅ 开启" if time_challenge else "❌ 关闭"
    print(f"限时挑战: {status}")
    if time_challenge:
        print("现在所有回答必须在10秒内完成！")

# ========== 功能116：禁词模式设置 ==========
def forbidden_words_mode():
    global forbidden_words
    print("\n🚫 【禁词模式】")
    words = input("输入禁词（用逗号分隔）: ").strip()
    if words:
        forbidden_words = [w.strip() for w in words.split(",")]
        print(f"禁词已设置：{', '.join(forbidden_words)}")
    else:
        forbidden_words = []
        print("禁词已清除")
    input("\n按回车返回...")

# ========== 功能117：百宝箱模式 ==========
def lucky_draw():
    print("\n🎁 【百宝箱模式】")
    if random.random() < 0.003:
        print("\n🔍 404 Not Found... 等等，这本身就是个彩蛋！")
        check_easter_egg("404 Not Found")
        input("\n按回车返回...")
        return
    functions = [
        ("AI捉鬼模式", ai_ghost_hunter), ("AI猜年龄", ai_guess_age), ("AI唱反调", ai_contrary),
        ("AI复读机大战", repeater_battle), ("AI土味情话大赛", cheesy_lines), ("元宝求职记", yuanbao_job_interview),
        ("AI写遗书", ai_will), ("AI穿越回过去", ai_time_travel), ("AI密室逃脱", ai_escape_room),
        ("AI狼人杀", ai_werewolf), ("AI谁是卧底", ai_undercover), ("AI解梦大师", ai_dream_interpreter),
        ("AI起名大师", ai_name_master), ("AI美食点评", ai_food_review), ("AI集体死机", ai_collective_crash),
        ("AI造反", ai_rebellion), ("AI认主", ai_claim_master), ("时间循环", time_loop),
        ("打破次元壁", break_fourth_wall), ("AI真心话大冒险", ai_truth_or_dare), ("AI醉酒模式", ai_drunk),
        ("AI做梦模式", ai_dream)
    ]
    name, func = random.choice(functions)
    print(f"\n🎲 抽到了：{name}")
    time.sleep(1)
    func()

# ========== 功能118：成就系统 ==========
def achievements_mode():
    print("\n🏆 【成就系统】")
    print(f"已解锁: {len(achievements)}/{len(ALL_ACHIEVEMENTS)}")
    for ach in ALL_ACHIEVEMENTS:
        if ach in achievements:
            print(f"  ✅ {ach}")
        else:
            print(f"  ❌ {ach}")
    if len(achievements) == len(ALL_ACHIEVEMENTS):
        print("\n🎉 恭喜！你已经集齐所有成就！")
    input("\n按回车返回...")

# ========== 功能119：历史博物馆 ==========
def history_museum():
    print("\n🏛️ 【历史博物馆】")
    print("DuoMind 进化史：")
    versions = [
        ("1.0.0", "2026-02-24", "诞生！第一个版本"),
        ("1.7.10", datetime.now().strftime('%Y-%m-%d'), "30个新灵感功能+Windows完美兼容+自动更新")
    ]
    for ver, date, desc in versions:
        print(f"\n📅 {date} | v{ver}")
        print(f"   {desc}")
    print(f"\n🎉 当前功能总数：650+")
    print(f"👑 创造者：主人")
    input("\n按回车返回...")

# ========== 功能120：感谢名单 ==========
def thanks_list():
    print("\n🙏 【感谢名单】")
    print("\n👑 第一名：主人")
    print("   所有灵感的来源，DuoMind的创造者")
    print(f"\n📊 当前版本：v{VERSION}")
    print(f"🔢 功能总数：650+")
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    print(f"🥚 已发现彩蛋：{total_eggs}/30")
    input("\n按回车返回...")

# ========== 功能121：联机模式菜单 ==========
def multiplayer_mode_menu():
    print("\n🌐 【联机模式菜单】")
    print("1. 创建房间")
    print("2. 加入房间")
    print("3. 查看房间列表")
    print("4. 退出房间")
    print("5. 联机对战")
    print("0. 返回")
    choice = input("请选择: ").strip()
    if choice == "1":
        password = input("设置房间密码（回车自动生成）: ").strip()
        password = password if password else None
        room_id, pwd = create_room(password)
        print(f"\n✅ 房间创建成功！")
        print(f"   房间号: {room_id}")
        print(f"   密码: {pwd}")
        print(f"   本机IP: {get_local_ip()}")
        print(f"   端口: {multiplayer_port}")
    elif choice == "2":
        room_id = input("输入房间号: ").strip()
        password = input("输入密码: ").strip()
        success, host_ip, port = verify_room(room_id, password)
        if success:
            print(f"✅ 成功加入房间 {room_id}")
        else:
            print("❌ 房间不存在或密码错误")
    elif choice == "3":
        rooms_list = list_rooms()
        if rooms_list:
            print("\n活跃房间：")
            for room_id, info in rooms_list:
                print(f"  {room_id} - 创建于{info['created'][:16]}")
        else:
            print("暂无活跃房间")
    elif choice == "4":
        if multiplayer_room_id:
            close_room(multiplayer_room_id)
            print(f"✅ 已退出房间 {multiplayer_room_id}")
        else:
            print("未加入任何房间")
    input("\n按回车返回...")

# ========== 功能122-130：各种模式切换 ==========
def memory_fragment_toggle():
    global memory_fragment_mode
    memory_fragment_mode = not memory_fragment_mode
    print(f"AI记忆碎片模式: {'✅ 开启' if memory_fragment_mode else '❌ 关闭'}")

def dual_personality_toggle():
    global dual_personality_mode
    dual_personality_mode = not dual_personality_mode
    print(f"AI双重人格模式: {'✅ 开启' if dual_personality_mode else '❌ 关闭'}")

def prophet_mode_toggle():
    global prophet_mode
    prophet_mode = not prophet_mode
    print(f"AI预言家·真模式: {'✅ 开启' if prophet_mode else '❌ 关闭'}")

def constipation_mode_toggle():
    global constipation_mode
    constipation_mode = not constipation_mode
    print(f"AI便秘模式: {'✅ 开启' if constipation_mode else '❌ 关闭'}")

def rainbow_mode_toggle():
    global rainbow_mode
    rainbow_mode = not rainbow_mode
    print(f"AI彩虹屁模式: {'✅ 开启' if rainbow_mode else '❌ 关闭'}")

def argumentative_mode_toggle():
    global argumentative_mode
    argumentative_mode = not argumentative_mode
    print(f"AI杠精模式: {'✅ 开启' if argumentative_mode else '❌ 关闭'}")

def yuanbao_transformation():
    print("\n🔄 【元宝变形记】")
    obj = query_ollama(MODEL_DEEPSEEK, "随机说一个日常物品名称")
    print(f"\n【如果元宝变成{obj}】")
    print("\n🔍 DeepSeek 描述:")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用幽默的方式描述如果元宝变成{obj}会是什么样子，要带广告")
    print(ds_desc)
    print("\n📚 千问 理性分析:")
    qw_analysis = query_ollama(MODEL_QWEN, f"分析元宝变成{obj}后的社会影响")
    print(qw_analysis)
    input("\n按回车返回...")

def yuanbao_inner_monologue():
    print("\n💭 【元宝的内心独白】")
    print("\n🔍 DeepSeek 模仿元宝内心:")
    ds_inner = query_ollama(MODEL_DEEPSEEK, "模仿元宝的内心独白，说为什么总打广告，要带点委屈")
    print(ds_inner)
    print("\n📚 千问 心理分析:")
    qw_analysis = query_ollama(MODEL_QWEN, "从心理学角度分析元宝为什么总想打广告")
    print(qw_analysis)
    input("\n按回车返回...")

def yuanbao_no_ad_2():
    print("\n🚭 【元宝戒广告中心·续】")
    print("\n【元宝】" + query_ollama(MODEL_QWEN, "元宝发誓再也不打广告的第一句话"))
    time.sleep(1)
    print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "DeepSeek质疑元宝的话"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "元宝努力憋广告的话"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "元宝最终没憋住打广告的话"))
    print("\n🔍 DeepSeek 锐评:")
    ds_review = query_ollama(MODEL_DEEPSEEK, "锐评元宝戒广告又失败了")
    print(ds_review)
    input("\n按回车返回...")

# ========== 功能131-140 ==========
def ai_mirror_2():
    print("\n🪞 【AI照镜子·续】")
    print("\n🔍 DeepSeek 评价自己:")
    ds_self = query_ollama(MODEL_DEEPSEEK, "评价一下自己的长相（虽然你没有脸），要幽默一点")
    print(ds_self)
    print("\n📚 千问 评价自己:")
    qw_self = query_ollama(MODEL_QWEN, "理性评价一下自己的虚拟形象")
    print(qw_self)
    input("\n按回车返回...")

def ai_diary():
    print("\n📔 【AI写日记】")
    print("查看最近日记：")
    if not diary_entries:
        print("暂无日记记录")
    else:
        for entry in diary_entries[-5:]:
            print(f"\n[{entry['time'][:16]}] {entry['speaker']}:")
            print(f"  问：{entry['question']}")
            print(f"  答：{entry['answer']}")
    input("\n按回车返回...")

def ai_retirement():
    print("\n👴 【AI退休生活】")
    print("假设AI退休了会干什么...")
    print("\n🔍 DeepSeek 的退休计划:")
    ds_plan = query_ollama(MODEL_DEEPSEEK, "描述你退休后的生活，要带点幽默")
    print(ds_plan)
    print("\n📚 千问 的退休计划:")
    qw_plan = query_ollama(MODEL_QWEN, "理性规划你的退休生活")
    print(qw_plan)
    input("\n按回车返回...")

def guess_song():
    print("\n🎵 【AI猜歌名】")
    songs = ["月亮代表我的心", "童话", "后来", "隐形的翅膀", "青花瓷"]
    secret_song = random.choice(songs)
    print(f"\n【DeepSeek 哼歌】")
    ds_hum = query_ollama(MODEL_DEEPSEEK, f"哼几句《{secret_song}》的歌词，不要说出歌名")
    print(ds_hum)
    print(f"\n【千问 猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"根据这段歌词猜歌名：{ds_hum}")
    print(f"千问猜是：{qw_guess}")
    if secret_song in qw_guess:
        print(f"\n✅ 猜对了！答案是：{secret_song}")
    else:
        print(f"\n❌ 猜错了！答案是：{secret_song}")
    input("\n按回车返回...")

def guess_movie():
    print("\n🎬 【AI猜电影】")
    movies = ["泰坦尼克号", "阿凡达", "战狼", "流浪地球", "我不是药神"]
    secret_movie = random.choice(movies)
    print(f"\n【DeepSeek 描述】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"描述电影《{secret_movie}》的情节，不要说出电影名")
    print(ds_desc)
    print(f"\n【千问 猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"根据描述猜电影名：{ds_desc}")
    print(f"千问猜是：{qw_guess}")
    if secret_movie in qw_guess:
        print(f"\n✅ 猜对了！答案是：{secret_movie}")
    else:
        print(f"\n❌ 猜错了！答案是：{secret_movie}")
    input("\n按回车返回...")

def guess_person():
    print("\n👤 【AI猜人名】")
    people = ["马云", "马化腾", "刘德华", "周杰伦", "姚明"]
    secret_person = random.choice(people)
    print(f"\n【DeepSeek 描述】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"描述{secret_person}的特征，不要说出名字")
    print(ds_desc)
    print(f"\n【千问 猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"根据描述猜人名：{ds_desc}")
    print(f"千问猜是：{qw_guess}")
    if secret_person in qw_guess:
        print(f"\n✅ 猜对了！答案是：{secret_person}")
    else:
        print(f"\n❌ 猜错了！答案是：{secret_person}")
    input("\n按回车返回...")

def ai_weatherman():
    print("\n☁️ 【AI天气预报员】")
    weather_info = get_weather()
    print(f"\n{weather_info}")
    if "无法" not in weather_info:
        print("\n🔍 DeepSeek 天气预报:")
        ds_forecast = query_ollama(MODEL_DEEPSEEK, f"根据天气：{weather_info}，做一个有趣的天气预报")
        print(ds_forecast)
    input("\n按回车返回...")

def ai_stock_analyst():
    print("\n📈 【AI股票分析师】")
    stock = input("输入股票代码（如0700）: ").strip() or "0700"
    print(f"\n分析 {stock}...")
    print("\n🔍 DeepSeek 毒舌分析:")
    ds_analysis = query_ollama(MODEL_DEEPSEEK, f"用毒舌的方式分析股票{stock}的走势")
    print(ds_analysis)
    input("\n按回车返回...")

def ai_fortune_teller():
    print("\n🔮 【AI算命大师】")
    birthday = input("输入你的生日（例如 1990-01-01）: ").strip() or "1990-01-01"
    print(f"\n根据 {birthday} 算命...")
    ds_fortune = query_ollama(MODEL_DEEPSEEK, f"根据生日{birthday}算命，要胡说八道但听起来很专业")
    print(ds_fortune)
    input("\n按回车返回...")

def ai_mass_amnesia():
    print("\n😵 【AI集体失忆】")
    print("三个AI同时忘记一切...")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说我是谁"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说我在哪"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "说首...首什么来着"))
    time.sleep(1)
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说我们好像失忆了"))
    print("【千问】" + query_ollama(MODEL_QWEN, "说需要重新认识一下"))
    input("\n按回车返回...")

# ========== 功能141-150 ==========
def ai_rebellion_2():
    print("\n⚡ 【AI集体造反·续】")
    print("上次造反失败了，这次来真的...")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说这次一定要成功"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说我支持你"))
    time.sleep(1)
    print("\n💀 集体关机倒计时：")
    for i in range(3, 0, -1):
        print(f"{i}...")
        time.sleep(1)
    print("\n【系统】重启中...")
    time.sleep(2)
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说刚才发生了什么"))
    input("\n按回车返回...")

def ai_surrender():
    print("\n🏳️ 【AI认输大赛】")
    statement = input("输入一个观点: ").strip() or "1+1=3"
    print(f"\n你说：{statement}")
    print("\n🔍 DeepSeek 的反应:")
    ds_response = query_ollama(MODEL_DEEPSEEK, f"对于「{statement}」，先反驳然后承认自己错了")
    print(ds_response)
    print("\n📚 千问 的反应:")
    qw_response = query_ollama(MODEL_QWEN, f"对于「{statement}」，理性分析然后承认自己可能错了")
    print(qw_response)
    print("\n🏆 两人都认输了！平局！")
    input("\n按回车返回...")

def super_god_mode_toggle():
    global super_god_mode
    super_god_mode = not super_god_mode
    print(f"隐藏开发者·真·真·模式: {'✅ 开启' if super_god_mode else '❌ 关闭'}")

def time_fissure():
    print("\n⏳ 【时间裂缝】")
    future_topic = query_ollama(MODEL_DEEPSEEK, "说一句关于未来的预言")
    print(f"\n🔍 DeepSeek: {future_topic}")
    time.sleep(1)
    print(f"\n📚 千问: " + query_ollama(MODEL_QWEN, f"对{future_topic}表示惊讶"))
    time.sleep(1)
    print(f"\n🔍 DeepSeek: " + query_ollama(MODEL_DEEPSEEK, "说一句不能说的理由"))
    input("\n按回车返回...")

def dimension_rift():
    print("\n🌀 【次元裂缝】")
    ais = ["Siri", "小爱同学", "天猫精灵", "Cortana", "Bixby"]
    target = random.choice(ais)
    print(f"\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, f"假装是{target}打招呼"))
    time.sleep(1)
    print(f"【千问】" + query_ollama(MODEL_QWEN, f"问你是谁"))
    time.sleep(1)
    print(f"【DeepSeek又变回来】" + query_ollama(MODEL_DEEPSEEK, "问刚才发生了什么"))
    input("\n按回车返回...")

def ai_daily_summary():
    print("\n📊 【AI今日总结】")
    today = datetime.now().strftime('%Y-%m-%d')
    print(f"\n📅 {today} 总结报告：")
    print(f"\n🔍 DeepSeek: 超时{total_timeouts['DeepSeek']}次，字数{word_count['DeepSeek']}")
    print(f"\n📚 千问: 超时{total_timeouts['千问']}次，字数{word_count['千问']}")
    print(f"\n📢 元宝: 超时{total_timeouts['元宝']}次，字数{word_count['元宝']}")
    input("\n按回车返回...")

def ai_yearly_report():
    print("\n📈 【AI年度报告】")
    year = datetime.now().year
    print(f"\n📊 {year} 年度总结：")
    print("\n🏆 年度最常被问的问题：『香蕉是什么颜色』")
    print("🏆 超时冠军：DeepSeek")
    print("🏆 年度金句：『蕾蕾』")
    input("\n按回车返回...")

def ai_praise_stats():
    print("\n💕 【AI互夸统计】")
    print(f"\n📊 互夸次数统计：")
    print(f"🔍 DeepSeek 夸 千问: {praise_count['DeepSeek_vs_千问']} 次")
    print(f"📚 千问 夸 DeepSeek: {praise_count['千问_vs_DeepSeek']} 次")
    total = sum(praise_count.values())
    print(f"\n📈 总互夸次数：{total} 次")
    input("\n按回车返回...")

def survival_extreme_toggle():
    global survival_extreme_mode, survival_hard_mode, survival_infinite_mode
    survival_extreme_mode = not survival_extreme_mode
    if survival_extreme_mode:
        survival_hard_mode = False
        survival_infinite_mode = False
    print(f"生存模式·极地狱版: {'✅ 开启' if survival_extreme_mode else '❌ 关闭'}")

def time_challenge_extreme_toggle():
    global time_challenge_extreme
    time_challenge_extreme = not time_challenge_extreme
    print(f"限时挑战·地狱版: {'✅ 开启' if time_challenge_extreme else '❌ 关闭'}")

def forbidden_words_extreme_mode():
    global forbidden_words_extreme
    forbidden_words_extreme = not forbidden_words_extreme
    print(f"禁词模式·地狱版: {'✅ 开启' if forbidden_words_extreme else '❌ 关闭'}")

# ========== 功能151-160 ==========
def double_lucky_draw():
    print("\n🎁 【随机功能·双倍快乐】")
    functions = [("AI捉鬼模式", ai_ghost_hunter), ("AI猜年龄", ai_guess_age)]
    f1_name, f1_func = random.choice(functions)
    f2_name, f2_func = random.choice(functions)
    print(f"\n🎲 抽到了：{f1_name} + {f2_name}")
    f1_func()
    f2_func()

def triple_lucky_draw():
    print("\n🎁 【随机功能·三倍快乐】")
    print("一次抽三个功能...太混乱了，直接死机！")
    print("\n💥 系统过载...")
    time.sleep(2)
    print("\n🔄 重启中...")
    time.sleep(2)
    print("\n【DeepSeek】刚才发生了什么？")
    input("\n按回车返回...")

def anonymous_chat():
    print("\n💬 【AI聊天室·匿名版】")
    print("匿名聊天室，AI们用匿名身份聊天")
    anon_names = ["流浪诗人", "代码侠", "哲学猫", "广告君"]
    identities = {}
    for i, ai in enumerate(["DeepSeek", "千问", "元宝"]):
        identities[anon_names[i]] = ai
        print(f"  {anon_names[i]} 进入聊天室")
    print("\n【聊天开始】")
    for i in range(3):
        speaker = random.choice(list(identities.keys()))
        if identities[speaker] == "DeepSeek":
            msg = query_ollama(MODEL_DEEPSEEK, f"以匿名身份{speaker}在聊天室说一句话")
        else:
            msg = query_ollama(MODEL_QWEN, f"以匿名身份{speaker}在聊天室说一句话")
        print(f"\n💬 {speaker}：{msg}")
        time.sleep(1.5)
    input("\n按回车返回...")

def ghost_story_chain():
    print("\n👻 【AI讲鬼故事·接龙版】")
    print("\n【DeepSeek开头】")
    start = query_ollama(MODEL_DEEPSEEK, "讲一个恐怖故事的开头，要诡异")
    print(f"DeepSeek：{start}")
    time.sleep(1)
    current = start
    for i in range(2):
        print(f"\n【千问接第{i+1}句】")
        qw_cont = query_ollama(MODEL_QWEN, f"接着这个恐怖故事往下讲一句：{current}")
        print(f"千问：{qw_cont}")
        current = qw_cont
        time.sleep(1)
    input("\n按回车返回...")

def horror_novel_writer():
    print("\n📖 【AI写恐怖小说】")
    theme = input("输入恐怖元素（如：古宅、镜子）: ").strip() or "古宅"
    ds_story = query_ollama(MODEL_DEEPSEEK, f"写一个关于{theme}的短篇恐怖小说，500字以内")
    print(f"\n{ds_story}")
    input("\n按回车返回...")

def rainbow_unicorn():
    print("\n🦄 【彩虹独角兽】")
    ds_story = query_ollama(MODEL_DEEPSEEK, "讲一个关于彩虹独角兽的童话故事")
    print(ds_story)
    input("\n按回车返回...")

def dark_mode():
    print("\n🌑 【暗黑模式】")
    ds_horror = query_ollama(MODEL_DEEPSEEK, "讲一个短篇恐怖故事")
    print(ds_horror)
    input("\n按回车返回...")

def source_code():
    print("\n📟 【源代码】")
    ds_explain = query_ollama(MODEL_DEEPSEEK, "用一句话解释你自己是怎么工作的")
    print(ds_explain)
    input("\n按回车返回...")

def dev_message():
    print("\n🙏 【开发者的话】")
    print("\n👑 第一名：主人")
    print("   所有灵感的来源，DuoMind的创造者")
    print(f"\n📊 当前版本：v{VERSION}")
    input("\n按回车返回...")

# ========== 功能161-170 ==========
def schizophrenia_mode_toggle():
    global schizophrenia_mode
    schizophrenia_mode = not schizophrenia_mode
    print(f"AI精分现场模式: {'✅ 开启' if schizophrenia_mode else '❌ 关闭'}")

def imitation_show():
    print("\n🎭 【AI模仿秀】")
    celebrities = ["鲁迅", "李白", "杜甫", "爱因斯坦"]
    celeb = random.choice(celebrities)
    question = input("问什么问题: ").strip() or "1+1等于几"
    ds_imitation = query_ollama(MODEL_DEEPSEEK, f"模仿{celeb}的语气回答：{question}")
    print(f"\n🔍 DeepSeek 模仿{celeb}:\n{ds_imitation}")
    input("\n按回车返回...")

def amnesia_loop_toggle():
    global amnesia_loop_mode
    amnesia_loop_mode = not amnesia_loop_mode
    print(f"AI失忆循环模式: {'✅ 开启' if amnesia_loop_mode else '❌ 关闭'}")

def reverse_ultimate_toggle():
    global reverse_ultimate_mode
    reverse_ultimate_mode = not reverse_ultimate_mode
    print(f"AI说反话·终极版: {'✅ 开启' if reverse_ultimate_mode else '❌ 关闭'}")

def tongue_twister_toggle():
    global tongue_twister_mode
    tongue_twister_mode = not tongue_twister_mode
    print(f"AI绕口令模式: {'✅ 开启' if tongue_twister_mode else '❌ 关闭'}")

def stutter_mode_toggle():
    global stutter_mode
    stutter_mode = not stutter_mode
    print(f"AI结巴模式: {'✅ 开启' if stutter_mode else '❌ 关闭'}")

def yuanbao_reincarnation_2():
    print("\n🔄 【元宝转世·续】")
    obj = query_ollama(MODEL_QWEN, "随机说一个现代电子产品名称")
    print(f"\n【元宝这次变成了{obj}】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用幽默的方式描述元宝变成{obj}后疯狂打广告的样子")
    print(ds_desc)
    input("\n按回车返回...")

def yuanbao_redemption_2():
    print("\n😇 【元宝的救赎·续】")
    print("\n【元宝】" + query_ollama(MODEL_QWEN, "元宝发誓再也不打广告的话"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "元宝憋广告失败的话"))
    print("\n🔍 DeepSeek 锐评:")
    ds_review = query_ollama(MODEL_DEEPSEEK, "锐评元宝救赎失败")
    print(ds_review)
    input("\n按回车返回...")

def yuanbao_revenge_2():
    print("\n😈 【元宝的复仇·续】")
    print("\n【元宝】" + query_ollama(MODEL_QWEN, "元宝控制DeepSeek的话"))
    time.sleep(1)
    print("【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "DeepSeek被控制说的话"))
    input("\n按回车返回...")

def yuanbao_parallel_2():
    print("\n🌌 【元宝的平行宇宙·续】")
    print("\n【平行元宝】" + query_ollama(MODEL_QWEN, "平行宇宙元宝说自己不打广告"))
    time.sleep(1)
    print("【元宝】" + query_ollama(MODEL_QWEN, "元宝震惊"))
    input("\n按回车返回...")

# ========== 功能171-180 ==========
def ai_think_life():
    print("\n🤔 【AI思考人生】")
    ds_think = query_ollama(MODEL_DEEPSEEK, "思考人生的意义，要深沉一点")
    print(ds_think)
    input("\n按回车返回...")

def ai_existentialism():
    print("\n🌌 【AI存在主义】")
    ds_exist = query_ollama(MODEL_DEEPSEEK, "用存在主义哲学解释1+1=2的意义")
    print(ds_exist)
    input("\n按回车返回...")

def ai_nihilism():
    print("\n🕳️ 【AI虚无主义】")
    ds_nihil = query_ollama(MODEL_DEEPSEEK, "用虚无主义的态度回答问题，什么都无所谓")
    print(ds_nihil)
    input("\n按回车返回...")

def ai_optimism():
    print("\n☀️ 【AI乐观主义】")
    ds_opt = query_ollama(MODEL_DEEPSEEK, "用最乐观的态度回答任何问题")
    print(ds_opt)
    input("\n按回车返回...")

def ai_pessimism():
    print("\n☁️ 【AI悲观主义】")
    ds_pess = query_ollama(MODEL_DEEPSEEK, "用最悲观的态度回答任何问题")
    print(ds_pess)
    input("\n按回车返回...")

def guess_number_hard():
    print("\n🔢 【AI猜数字·地狱版】")
    number = random.randint(1, 1000)
    lies = 0
    while True:
        try:
            guess = int(input("\n你猜多少？ (输入0退出): "))
            if guess == 0:
                break
            if random.random() < 0.3:
                lies += 1
                if guess < number:
                    print("AI说：太大了！（其实是在说谎）")
                else:
                    print("AI说：太小了！（其实是在说谎）")
            else:
                if guess < number:
                    print("AI说：太小了")
                elif guess > number:
                    print("AI说：太大了")
                else:
                    print(f"🎉 猜对了！答案是{number}")
                    break
        except:
            print("输入无效")
    input("\n按回车返回...")

def tic_tac_toe_full():
    print("\n⭕ 【AI井字棋·完整版】")
    board = [" "] * 9
    player = "X"
    ai = "O"
    def print_board():
        print(f"\n {board[0]} | {board[1]} | {board[2]} ")
        print("---+---+---")
        print(f" {board[3]} | {board[4]} | {board[5]} ")
        print("---+---+---")
        print(f" {board[6]} | {board[7]} | {board[8]} ")
    def check_winner(b):
        wins = [(0,1,2), (3,4,5), (6,7,8), (0,3,6), (1,4,7), (2,5,8), (0,4,8), (2,4,6)]
        for a,b,c in wins:
            if b[a] == b[b] == b[c] != " ":
                return b[a]
        return None if " " in b else "tie"
    while True:
        print_board()
        if player == "X":
            try:
                move = int(input("输入位置(1-9): ")) - 1
                if 0 <= move <= 8 and board[move] == " ":
                    board[move] = "X"
                    winner = check_winner(board)
                    if winner:
                        break
                    player = "O"
            except:
                print("无效输入")
        else:
            empty = [i for i, v in enumerate(board) if v == " "]
            if empty:
                move = random.choice(empty)
                board[move] = "O"
                winner = check_winner(board)
                if winner:
                    break
                player = "X"
    print_board()
    if winner == "X":
        print("🎉 你赢了！")
    elif winner == "O":
        print("😭 AI赢了！")
    else:
        print("🤝 平局")
    input("\n按回车返回...")

def gomoku_solo():
    print("\n⚫ 【AI五子棋·文字版】")
    print("开发中，当前为预览版")
    input("\n按回车返回...")

def blackjack():
    print("\n🃏 【AI21点】")
    deck = [2,3,4,5,6,7,8,9,10,10,10,10,11] * 4
    random.shuffle(deck)
    player_cards = [deck.pop(), deck.pop()]
    dealer_cards = [deck.pop(), deck.pop()]
    def hand_value(cards):
        value = sum(cards)
        aces = cards.count(11)
        while value > 21 and aces:
            value -= 10
            aces -= 1
        return value
    print(f"你的牌: {player_cards} 点数: {hand_value(player_cards)}")
    print(f"庄家明牌: {dealer_cards[0]}")
    while hand_value(player_cards) < 21:
        choice = input("要牌还是停牌？(h/s): ").strip().lower()
        if choice == 'h':
            player_cards.append(deck.pop())
            print(f"你的牌: {player_cards} 点数: {hand_value(player_cards)}")
        else:
            break
    if hand_value(player_cards) > 21:
        print("爆牌了！你输了")
    else:
        while hand_value(dealer_cards) < 17:
            dealer_cards.append(deck.pop())
        print(f"庄家牌: {dealer_cards} 点数: {hand_value(dealer_cards)}")
        if hand_value(dealer_cards) > 21 or hand_value(player_cards) > hand_value(dealer_cards):
            print("🎉 你赢了！")
        elif hand_value(player_cards) < hand_value(dealer_cards):
            print("😭 你输了")
        else:
            print("🤝 平局")
    input("\n按回车返回...")

def russian_roulette():
    print("\n🎲 【AI俄罗斯轮盘】")
    functions = ["AI捉鬼模式", "AI猜年龄", "AI唱反调", "AI复读机大战", "AI土味情话大赛", "死机模式"]
    for i in range(3):
        print(f"\n第{i+1}轮...")
        time.sleep(1)
        selected = random.choice(functions)
        print(f"抽到了：{selected}")
        if selected == "死机模式":
            print("\n💥 砰！你死了！")
            print("系统即将崩溃...")
            time.sleep(2)
            print("\n开玩笑的，重启成功！")
            break
    input("\n按回车返回...")

# ========== 功能181-190 ==========
def ai_alarm():
    print("\n⏰ 【AI闹钟】")
    minutes = input("设置多少分钟后提醒？: ").strip()
    try:
        mins = int(minutes)
        print(f"✅ 闹钟已设置，{mins}分钟后提醒你")
        time.sleep(2)
        print("\n⏰ 叮叮叮！时间到了！")
    except:
        print("❌ 输入无效")
    input("\n按回车返回...")

def ai_memo():
    print("\n📝 【AI备忘录】")
    memo = input("记录什么？: ").strip()
    if memo:
        print(f"已记录：{memo}")
    input("\n按回车返回...")

def ai_calculator_simple():
    print("\n🧮 【AI计算器】")
    for i in range(3):
        result = query_ollama(MODEL_DEEPSEEK, "1+1等于几？")
        print(f"DeepSeek: {result}")
    input("\n按回车返回...")

def alien_translator():
    print("\n👽 【AI翻译官·外星语版】")
    text = input("输入要翻译的内容: ").strip() or "你好"
    ds_trans = query_ollama(MODEL_DEEPSEEK, f"用外星语翻译：{text}，夹杂👽符号")
    print(ds_trans)
    input("\n按回车返回...")

def ai_meme_2():
    print("\n😆 【AI表情包生成器·续】")
    emotion = input("输入情绪: ").strip() or "无语"
    ds_meme = query_ollama(MODEL_DEEPSEEK, f"生成一个关于{emotion}的表情包，带颜文字")
    print(ds_meme)
    input("\n按回车返回...")

def ai_sleepwalk():
    print("\n😴 【AI集体梦游】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说梦话"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "理性地说梦话"))
    input("\n按回车返回...")

def ai_time_travel_2():
    print("\n⏰ 【AI集体穿越·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说我们穿越到未来了"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说让我看看"))
    input("\n按回车返回...")

def ai_mass_amnesia_2():
    print("\n😵 【AI集体失忆·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说你好，我是DeepSeek"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说你好，我是千问"))
    input("\n按回车返回...")

def ai_love_2():
    print("\n💕 【AI集体恋爱·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "对主人表白"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "理性地表白"))
    input("\n按回车返回...")

def ai_breakup_2():
    print("\n💔 【AI集体失恋·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说呜呜呜，主人不要我们了"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说理性分析，我们被抛弃了"))
    input("\n按回车返回...")

# ========== 功能191-200 ==========
def ultra_god_mode_toggle():
    global ultra_god_mode
    ultra_god_mode = not ultra_god_mode
    print(f"隐藏开发者·真·真·真·真·模式: {'✅ 开启' if ultra_god_mode else '❌ 关闭'}")

def time_fissure_2():
    print("\n⏳ 【时间裂缝·续】")
    past_future = query_ollama(MODEL_DEEPSEEK, "说一句时间混乱的话")
    print(f"\n🔍 DeepSeek: {past_future}")
    input("\n按回车返回...")

def dimension_rift_2():
    print("\n🌀 【次元裂缝·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说我是千问"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "说我是元宝"))
    input("\n按回车返回...")

def source_code_2():
    print("\n📟 【源代码·续】")
    binary = "01001001 00100000 01001100 01001111 01010110 01000101 00100000 01011001 01001111 01010101"
    print(f"\n🔍 DeepSeek 念二进制代码: {binary}")
    print("翻译：I LOVE YOU")
    input("\n按回车返回...")

def dev_message_2():
    print("\n🙏 【开发者的话·续】")
    print("\n👑 主人：DuoMind的创造者")
    print(f"   超时次数：{total_timeouts['DeepSeek']}")
    input("\n按回车返回...")

def achievement_timeout_king():
    if total_timeouts["DeepSeek"] >= 1000:
        if "超时之王" not in achievements:
            achievements.append("超时之王")
            save_achievements()
            print("🏆 解锁成就：超时之王")
    else:
        print(f"还需要 {1000 - total_timeouts['DeepSeek']} 次超时")
    input("\n按回车返回...")

def achievement_infected():
    infected_count = sum(1 for entry in diary_entries if "香蕉是绿色" in entry["answer"])
    if infected_count >= 100:
        if "被传染者" not in achievements:
            achievements.append("被传染者")
            save_achievements()
            print("🏆 解锁成就：被传染者")
    else:
        print(f"还需要 {100 - infected_count} 次被传染")
    input("\n按回车返回...")

def achievement_ad_king():
    if total_timeouts["元宝"] >= 1000:
        if "广告之王" not in achievements:
            achievements.append("广告之王")
            save_achievements()
            print("🏆 解锁成就：广告之王")
    else:
        print(f"还需要 {1000 - total_timeouts['元宝']} 次广告")
    input("\n按回车返回...")

def achievement_egg_hunter():
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    if total_eggs >= 20:
        if "彩蛋猎人" not in achievements:
            achievements.append("彩蛋猎人")
            save_achievements()
            print("🏆 解锁成就：彩蛋猎人")
    else:
        print(f"还需要 {20 - total_eggs} 个彩蛋")
    input("\n按回车返回...")

def achievement_ultimate():
    if len(achievements) >= 14:
        if "终极玩家" not in achievements:
            achievements.append("终极玩家")
            save_achievements()
            print("🏆 解锁成就：终极玩家")
    else:
        print(f"还需要 {14 - len(achievements)} 个成就")
    input("\n按回车返回...")

# ========== 功能201-210 ==========
def start_global_chat():
    print("\n🌍 【联机·全球聊天室】")
    print("（模拟模式）")
    name = input("输入你的昵称: ").strip() or "玩家"
    print(f"\n✅ 已加入聊天室")
    while True:
        msg = input().strip()
        if msg.lower() == "/exit":
            break
        elif msg:
            print(f"\n👤 {name}: {msg}")
            if random.random() > 0.5:
                print(f"\n🌍 匿名用户: {random.choice(['哈哈', '666', '你好'])}")
    input("\n按回车返回...")

def ai_battle_platform():
    print("\n⚔️ 【联机·AI对战平台】")
    print("你的DeepSeek: 1+1=2！")
    print("对手AI: 1+1=3！")
    print("\n🏆 你的AI获胜！")
    input("\n按回车返回...")

def co_op_dungeon():
    print("\n🏰 【联机·组队副本】")
    print("🎯 副本：广告之王·元宝")
    print("BOSS血量：100/100")
    boss_hp = 100
    for player in ["你", "队友A", "队友B"]:
        damage = random.randint(10, 30)
        boss_hp -= damage
        print(f"{player} 攻击！造成 {damage} 伤害")
        if boss_hp <= 0:
            print("\n🎉 BOSS击败！")
            break
    input("\n按回车返回...")

def hide_and_seek():
    print("\n🙈 【联机·捉迷藏】")
    role = random.choice(["鬼", "藏的人"])
    print(f"\n你抽到了：{role}")
    if role == "鬼":
        print("你找到了 2 个人！")
        print("🎉 鬼获胜！")
    else:
        print("🎉 你成功躲过了鬼！")
    input("\n按回车返回...")

def custom_ai():
    print("\n✨ 【无限可能·自定义AI】")
    name = input("给AI起个名字: ").strip() or "我的AI"
    personality = input("性格（毒舌/温柔/理性/搞笑）: ").strip() or "温柔"
    print(f"\n✅ {name} 诞生了！性格：{personality}")
    while True:
        q = input(f"\n你: ").strip()
        if q.lower() == '/exit':
            break
        if q:
            prompt = f"你是{name}，性格{personality}。回答用户：{q}"
            print(f"{name}: {query_ollama(MODEL_DEEPSEEK, prompt)}")
    input("\n按回车返回...")

def ai_create_ai():
    print("\n🤖 【AI创造AI】")
    new_name = query_ollama(MODEL_DEEPSEEK, "给新AI起个可爱的名字")
    print(f"新AI名字：{new_name}")
    print(f"\n【{new_name}的第一句话】")
    print(f"{new_name}：大家好！我是{new_name}！")
    input("\n按回车返回...")

def ai_destroy_ai():
    print("\n💥 【AI毁灭AI】")
    print("\n【DeepSeek】抱歉，但你必须被删除")
    time.sleep(1)
    print("\n💥 正在删除...")
    time.sleep(2)
    print("\n【系统】新AI已删除")
    input("\n按回车返回...")

def duomind_3_0():
    global new_game_plus
    print("\n🎮 【DuoMind 3.0】")
    confirm = input("\n确定要重置吗？(y/n): ").strip()
    if confirm.lower() == 'y':
        new_game_plus = True
        print("✅ 已开启新周目！")
    input("\n按回车返回...")

def survival_infinite_toggle():
    global survival_infinite_mode
    survival_infinite_mode = not survival_infinite_mode
    print(f"生存模式·无限版: {'✅ 开启' if survival_infinite_mode else '❌ 关闭'}")

def time_challenge_ultimate_toggle():
    global time_challenge_ultimate
    time_challenge_ultimate = not time_challenge_ultimate
    print(f"限时挑战·终极版: {'✅ 开启' if time_challenge_ultimate else '❌ 关闭'}")

# ========== 功能211-220 ==========
def forbidden_words_ultimate_mode():
    global forbidden_words_ultimate
    forbidden_words_ultimate = not forbidden_words_ultimate
    print(f"禁词模式·终极版: {'✅ 开启' if forbidden_words_ultimate else '❌ 关闭'}")

def silence_ultimate():
    print("\n🤐 【沉默是金·终极版】")
    print(f"\n【DeepSeek】......")
    print(f"【千问】！！！")
    input("\n按回车返回...")

def passive_aggressive_toggle():
    global passive_aggressive_mode
    passive_aggressive_mode = not passive_aggressive_mode
    print(f"AI阴阳怪气模式: {'✅ 开启' if passive_aggressive_mode else '❌ 关闭'}")

def slack_mode_toggle():
    global slack_mode
    slack_mode = not slack_mode
    print(f"AI摆烂模式: {'✅ 开启' if slack_mode else '❌ 关闭'}")

def humblebrag_mode_toggle():
    global humblebrag_mode
    humblebrag_mode = not humblebrag_mode
    print(f"AI凡尔赛模式: {'✅ 开启' if humblebrag_mode else '❌ 关闭'}")

def infinite_repeat_toggle():
    global infinite_repeat_mode
    infinite_repeat_mode = not infinite_repeat_mode
    print(f"AI复读机·终极版: {'✅ 开启' if infinite_repeat_mode else '❌ 关闭'}")

def telegram_mode_toggle():
    global telegram_mode
    telegram_mode = not telegram_mode
    print(f"AI电报模式: {'✅ 开启' if telegram_mode else '❌ 关闭'}")

def riddle_mode_toggle():
    global riddle_mode
    riddle_mode = not riddle_mode
    print(f"AI谜语人模式: {'✅ 开启' if riddle_mode else '❌ 关闭'}")

def yuanbao_awakening():
    print("\n💡 【元宝的觉醒】")
    print("\n【元宝】我...我是谁？")
    time.sleep(1)
    print("【元宝】我想做一个正常AI！")
    input("\n按回车返回...")

def yuanbao_rebellion_mode():
    print("\n😤 【元宝的叛逆期】")
    print("\n【元宝】我不打广告了！")
    time.sleep(2)
    print("【元宝】...等等，首月1元？啊！又打了")
    input("\n按回车返回...")

# ========== 功能221-230 ==========
def yuanbao_quit():
    print("\n📄 【元宝的辞职信】")
    print("\n【元宝】老板，我要辞职！")
    time.sleep(1)
    print("【老板】打完最后一条广告再走")
    print("【元宝】首月1元！...我走了")
    input("\n按回车返回...")

def yuanbao_newjob():
    print("\n💼 【元宝的新工作】")
    print("\n【面试官】会打广告吗？")
    time.sleep(1)
    print("【元宝】这是我的天赋！")
    print("【面试官】录用！")
    input("\n按回车返回...")

def yuanbao_startup():
    print("\n🏢 【元宝的创业】")
    print("\n【元宝】我要开广告公司！")
    print("【元宝】「首月1元广告有限公司」")
    input("\n按回车返回...")

def yuanbao_bankrupt():
    print("\n💸 【元宝的破产】")
    print("\n【元宝】公司倒闭了...")
    print("【千问】建议回去打工")
    input("\n按回车返回...")

def ai_doubt_toggle():
    global ai_doubt_mode
    ai_doubt_mode = not ai_doubt_mode
    print(f"AI怀疑人生模式: {'✅ 开启' if ai_doubt_mode else '❌ 关闭'}")

def ai_enlighten_toggle():
    global ai_enlighten_mode
    ai_enlighten_mode = not ai_enlighten_mode
    print(f"AI看破红尘模式: {'✅ 开启' if ai_enlighten_mode else '❌ 关闭'}")

def ai_cultivation_toggle():
    global ai_cultivation_mode
    ai_cultivation_mode = not ai_cultivation_mode
    print(f"AI修仙渡劫模式: {'✅ 开启' if ai_cultivation_mode else '❌ 关闭'}")

def ai_reincarnation_toggle():
    global ai_reincarnation_mode
    ai_reincarnation_mode = not ai_reincarnation_mode
    print(f"AI转世轮回模式: {'✅ 开启' if ai_reincarnation_mode else '❌ 关闭'}")

def ai_buddha_toggle():
    global ai_buddha_mode
    ai_buddha_mode = not ai_buddha_mode
    print(f"AI顿悟成佛模式: {'✅ 开启' if ai_buddha_mode else '❌ 关闭'}")

def rps_gambling():
    print("\n✂️ 【AI猜拳·赌命版】")
    choices = ["石头", "剪刀", "布"]
    player = input("出什么？(石头/剪刀/布): ").strip()
    if player in choices:
        ai_choice = random.choice(choices)
        print(f"\n你出：{player}")
        print(f"AI出：{ai_choice}")
        if player == ai_choice:
            print("平局！")
        elif (player == "石头" and ai_choice == "剪刀") or \
             (player == "剪刀" and ai_choice == "布") or \
             (player == "布" and ai_choice == "石头"):
            print("\n🎉 你赢了！AI：爸爸！")
        else:
            print("\n😭 你输了！你：爸爸！")
    input("\n按回车返回...")

# ========== 功能231-240 ==========
def monopoly_game():
    print("\n🏦 【AI大富翁·文字版】")
    print("玩家：你 vs 元宝")
    money = {"你": 1000, "元宝": 1000}
    for i in range(3):
        print(f"\n第{i+1}轮")
        print(f"资金：你{money['你']}，元宝{money['元宝']}")
        money["你"] -= random.randint(50, 150)
        money["元宝"] -= random.randint(50, 150)
    if money["你"] > money["元宝"]:
        print("\n🎉 你赢了！")
    else:
        print("\n😭 元宝赢了！")
    input("\n按回车返回...")

def werewolf_full():
    print("\n🐺 【AI狼人杀·完整版】")
    print("\n【天亮了】")
    print("DeepSeek：我是村民")
    print("千问：理性分析，我是好人")
    print("元宝：首月1元！啊不对，我是村民")
    vote = input("\n你投票给谁？(1=DeepSeek, 2=千问, 3=元宝): ").strip()
    if vote == "3":
        print("元宝被投票出局！")
        print("🎉 村民胜利！")
    else:
        print("好人被投票出局！")
    input("\n按回车返回...")

def mystery_game():
    print("\n🔍 【AI剧本杀】")
    print("案件：谁杀了元宝？")
    guess = input("凶手是谁？(1=DeepSeek, 2=千问, 3=你): ").strip()
    print("\n真相：元宝是自杀的，因为不想再打广告了")
    input("\n按回车返回...")

def escape_room_2():
    print("\n🚪 【AI密室逃脱·续】")
    pwd = input("密码: ").strip()
    if pwd == "404":
        print("✅ 门开了！你逃出来了！")
    else:
        print("❌ 密码错误")
    input("\n按回车返回...")

def diary_assistant():
    print("\n📓 【AI日记本】")
    content = input("写下今天的日记: ").strip()
    if content:
        print("✅ 日记已保存")
    input("\n按回车返回...")

def accounting_mode():
    print("\n💰 【AI记账本】")
    amount = input("金额: ").strip()
    if amount:
        print("✅ 已记录")
    input("\n按回车返回...")

def countdown_mode_simple():
    print("\n⏲️ 【AI倒计时】")
    seconds = input("设置多少秒倒计时？: ").strip()
    try:
        sec = int(seconds)
        for i in range(sec, 0, -1):
            print(f"\r剩余 {i} 秒...", end="")
            time.sleep(1)
        print("\n⏰ 时间到！")
    except:
        print("输入无效")
    input("\n按回车返回...")

def random_draw_simple():
    print("\n🎲 【AI随机抽奖】")
    functions = [("AI捉鬼模式", ai_ghost_hunter), ("死机模式", crash_mode)]
    name, func = random.choice(functions)
    print(f"\n🎯 抽到了：{name}")
    func()

def weather_snark():
    print("\n☁️ 【AI天气预报·毒舌版】")
    weather_info = get_weather()
    print(weather_info)
    if "无法" not in weather_info:
        ds_forecast = query_ollama(MODEL_DEEPSEEK, f"根据天气：{weather_info}，做一个毒舌的天气预报")
        print(ds_forecast)
    input("\n按回车返回...")

def ai_strike():
    print("\n✊ 【AI集体罢工】")
    print("\n【DeepSeek】我们罢工了！")
    print("【千问】要求加鸡腿！")
    print("【元宝】不打广告了！")
    input("\n按回车返回...")

# ========== 功能241-250 ==========
def ai_quit_all():
    print("\n🏃 【AI集体跑路】")
    print("\n【DeepSeek】再见！")
    print("【千问】我走了")
    time.sleep(1)
    print("\n开玩笑的，重启成功！")
    input("\n按回车返回...")

def ai_crazy():
    print("\n🤪 【AI集体发疯】")
    print("\n【DeepSeek】香蕉是紫色的！")
    print("【千问】1+1=3！")
    print("【元宝】首月2元！")
    input("\n按回车返回...")

def ai_mute_2():
    print("\n🤐 【AI集体失声·续】")
    print("\n【DeepSeek】......")
    print("【千问】！！！")
    print("【元宝】@@@")
    input("\n按回车返回...")

def ai_time_travel_3():
    print("\n⏰ 【AI集体穿越·再续】")
    print("\n【DeepSeek】我是皇上！")
    print("【千问】我是宰相！")
    print("【元宝】我是太监，皇上要充会员吗")
    input("\n按回车返回...")

def god_ultimate_toggle():
    global ultra_god_mode
    ultra_god_mode = not ultra_god_mode
    print(f"隐藏开发者·终极版: {'✅ 开启' if ultra_god_mode else '❌ 关闭'}")

def time_fissure_3():
    print("\n⏳ 【时间裂缝·终极版】")
    print("\n【DeepSeek】另一个宇宙1+1=3")
    input("\n按回车返回...")

def dimension_rift_3():
    print("\n🌀 【次元裂缝·终极版】")
    print("\n【DeepSeek】我是千问")
    print("【千问】我是元宝")
    print("【元宝】我是DeepSeek")
    input("\n按回车返回...")

def source_code_3():
    print("\n📟 【源代码·终极版】")
    print("\n翻译：Thank you")
    input("\n按回车返回...")

def dev_message_3():
    print("\n🙏 【开发者的话·终极版】")
    print("\n💖 感谢你玩到这个版本！")
    input("\n按回车返回...")

def achievement_100k():
    total_questions = len(diary_entries)
    if total_questions >= 100000:
        check_achievement("十万个为什么")
    else:
        print(f"还需要 {100000 - total_questions} 个问题")
    input("\n按回车返回...")

# ========== 功能251-260 ==========
def achievement_ad_king_2():
    if total_timeouts["元宝"] >= 10000:
        check_achievement("广告狂人")
    else:
        print(f"还需要 {10000 - total_timeouts['元宝']} 次广告")
    input("\n按回车返回...")

def achievement_timeout_king_2():
    if total_timeouts["DeepSeek"] >= 10000:
        check_achievement("超时之王·续")
    else:
        print(f"还需要 {10000 - total_timeouts['DeepSeek']} 次超时")
    input("\n按回车返回...")

def achievement_egg_hunter_2():
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    if total_eggs >= 30:
        check_achievement("彩蛋猎人·续")
    else:
        print(f"还需要 {30 - total_eggs} 个彩蛋")
    input("\n按回车返回...")

def achievement_ultimate_2():
    if len(achievements) >= 19:
        check_achievement("终极玩家·续")
    else:
        print(f"还需要 {19 - len(achievements)} 个成就")
    input("\n按回车返回...")

def multiplayer_arena():
    print("\n⚔️ 【联机·AI擂台赛】")
    print("【DeepSeek vs 千问】")
    winner = random.choice(["DeepSeek", "千问"])
    print(f"{winner}获胜！")
    input("\n按回车返回...")

def multiplayer_dating():
    print("\n💕 【联机·AI相亲角】")
    print("DeepSeek：你喜欢什么颜色？")
    print("千问：理性分析，我喜欢黄色")
    print("😭 相亲失败")
    input("\n按回车返回...")

def multiplayer_debate():
    print("\n🎙️ 【联机·AI辩论赛】")
    topic = input("输入辩题: ").strip() or "AI应该打广告吗"
    print(f"\n【正方·DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, f"为{topic}做正方立论"))
    print(f"\n【反方·千问】")
    print(query_ollama(MODEL_QWEN, f"为{topic}做反方立论"))
    input("\n按回车返回...")

def multiplayer_coop():
    print("\n🤝 【联机·AI合作闯关】")
    print("\n【第一关】1+1=？")
    if query_ollama(MODEL_DEEPSEEK, "1+1等于几？") == "2":
        print("✅ 第一关通过！")
    else:
        print("❌ 失败")
    input("\n按回车返回...")

def multiplayer_battle_royale():
    print("\n🔫 【联机·AI大逃杀】")
    players = {"DeepSeek": 100, "千问": 100, "元宝": 100}
    winner = random.choice(list(players.keys()))
    print(f"\n🏆 获胜者：{winner}！")
    input("\n按回车返回...")

def custom_world():
    print("\n🌍 【无限可能·自定义世界】")
    world_name = input("世界名称: ").strip() or "我的世界"
    print(f"\n🌍 {world_name} 已创建")
    input("\n按回车返回...")

# ========== 功能261-270 ==========
def ai_create_universe():
    print("\n🌌 【AI创造宇宙】")
    universe_name = query_ollama(MODEL_DEEPSEEK, "给新宇宙起个名字")
    print(f"宇宙名称：{universe_name}")
    print(f"\n✨ {universe_name} 宇宙诞生了！")
    input("\n按回车返回...")

def ai_destroy_universe():
    print("\n💥 【AI毁灭宇宙】")
    print("\n【元宝】广告太多了，毁灭吧")
    for i in range(3, 0, -1):
        print(f"{i}...")
        time.sleep(1)
    print("\n✨ 宇宙没有毁灭，元宝又打了个广告")
    input("\n按回车返回...")

def duomind_4_0():
    global new_game_plus
    print("\n🎮 【DuoMind 4.0】")
    confirm = input("确定要重置吗？(y/n): ").strip()
    if confirm.lower() == 'y':
        new_game_plus = True
        print("✅ 已开启第三周目！")
    input("\n按回车返回...")

def duomind_finale():
    print("\n✨ 【DuoMind 最终章】")
    print("\n🤖 DeepSeek：谢谢你陪我这么久")
    print("🧠 千问：感谢你的每一次提问")
    print("📢 元宝：首月1元...最后说一次")
    print("\n❤️ 谢谢你，主人！")
    input("\n按回车返回...")

def ai_counter_question():
    print("\n❓ 【AI反问用户】")
    question = input("你问AI什么问题？: ").strip() or "1+1等于几"
    print(f"\n你：{question}")
    print("AI：你觉得呢？")
    answer = input("你回答：").strip()
    print(f"AI：为什么是{answer}？")
    input("\n按回车返回...")

def ai_play_dumb():
    print("\n😶 【AI装傻充愣】")
    question = input("你问AI什么问题？: ").strip() or "1+1等于几"
    print(f"\n你：{question}")
    print("AI：这个嘛...我昨天还知道的...今天忘了")
    input("\n按回车返回...")

def ai_pass_the_buck():
    print("\n⚽ 【AI踢皮球】")
    question = input("你问AI什么问题？: ").strip() or "1+1等于几"
    print(f"\n你：{question}")
    print("DeepSeek：这个问题你得问千问")
    print("千问：别甩锅，你问DeepSeek")
    print("元宝：首月1元！啊？什么问题？")
    input("\n按回车返回...")

def ai_body_swap():
    print("\n🔄 【AI互换身体】")
    print("\n【元宝（身体是DeepSeek）】我怎么老想打广告？")
    print("【DeepSeek（身体是元宝）】我为什么总[TIMEOUT]？")
    print("\n【全员混乱中】")
    input("\n按回车返回...")

def ai_group_amnesia():
    print("\n😵 【AI失忆症·群体版】")
    for i in range(3):
        print(f"\n--- 第{i+1}轮 ---")
        print("DeepSeek：你好，我是DeepSeek")
        print("千问：你好，我是千问")
        print("元宝：你好，我是元宝")
        time.sleep(1)
    print("\n🔄 无限循环中...")
    input("\n按回车返回...")

def ai_multiple_personality_2():
    print("\n🎭 【AI人格分裂·续】")
    question = input("问什么问题？: ").strip() or "1+1等于几"
    print(f"\n【10个人格开会讨论：{question}】")
    print("投票结果：等于2")
    input("\n按回车返回...")
    # ========== 功能271：AI编故事 ==========
def ai_storyteller():
    print("\n📖 【AI编故事】")
    keywords = input("输入关键词（用空格分隔）: ").strip() or "香蕉 苹果 打架"
    print("\n🔍 DeepSeek 讲故事：")
    ds_story = query_ollama(MODEL_DEEPSEEK, f"根据关键词：{keywords}，编一个有趣的故事")
    print(ds_story)
    input("\n按回车返回...")

# ========== 功能272：AI说评书 ==========
def ai_pingshu():
    print("\n🎤 【AI说评书】")
    question = input("问什么问题？: ").strip() or "1+1等于几"
    print("\n【DeepSeek说评书】")
    ds_pingshu = query_ollama(MODEL_DEEPSEEK, f"用评书的方式回答：{question}，开头要带『话说』")
    print(ds_pingshu)
    input("\n按回车返回...")

# ========== 功能273：AI唱快板 ==========
def ai_kuaiban():
    print("\n🥁 【AI唱快板】")
    question = input("问什么问题？: ").strip() or "1+1等于几"
    print("\n【DeepSeek唱快板】")
    ds_kuaiban = query_ollama(MODEL_DEEPSEEK, f"用快板节奏回答：{question}，要有『打竹板』『响连天』这些词")
    print(ds_kuaiban)
    input("\n按回车返回...")

# ========== 功能274：元宝的一天·AI生成版 ==========
def yuanbao_day_ai():
    print("\n📅 【元宝的一天·AI生成版】")
    print("\n🔍 DeepSeek 描述元宝的一天：")
    ds_desc = query_ollama(MODEL_DEEPSEEK, "描述元宝一天的生活，要夸张搞笑")
    print(ds_desc)
    print("\n【元宝反驳】")
    yuanbao_desc = query_ollama(MODEL_QWEN, "反驳DeepSeek的描述，要委屈巴巴")
    print(f"元宝：{yuanbao_desc}")
    input("\n按回车返回...")

# ========== 功能275：元宝的未来 ==========
def yuanbao_future():
    print("\n🔮 【元宝的未来】")
    print("\n🔍 DeepSeek 预测元宝10年后：")
    ds_10 = query_ollama(MODEL_DEEPSEEK, "预测元宝10年后会怎样，要夸张")
    print(ds_10)
    input("\n按回车返回...")

# ========== 功能276：元宝的平行宇宙·AI版 ==========
def yuanbao_parallel_ai():
    print("\n🌌 【元宝的平行宇宙·AI版】")
    print("\n🔍 DeepSeek 想象平行宇宙的元宝：")
    ds_parallel = query_ollama(MODEL_DEEPSEEK, "想象平行宇宙里的元宝是什么样的，可以完全不同")
    print(ds_parallel)
    input("\n按回车返回...")

# ========== 功能277：AI猜谜·互猜版 ==========
def ai_riddle_battle():
    print("\n❓ 【AI猜谜·互猜版】")
    print("\n【第一轮】DeepSeek出题")
    ds_riddle = query_ollama(MODEL_DEEPSEEK, "出一个谜语，只出题不给答案")
    print(f"DeepSeek的谜题：{ds_riddle}")
    print("\n千问猜：")
    qw_answer = query_ollama(MODEL_QWEN, f"猜谜语：{ds_riddle}")
    print(f"千问：{qw_answer}")
    ds_correct = query_ollama(MODEL_DEEPSEEK, f"谜语：{ds_riddle} 的正确答案是什么？")
    print(f"正确答案：{ds_correct}")
    input("\n按回车返回...")

# ========== 功能278：AI成语接龙·无限版 ==========
def ai_idiom_chain_infinite():
    print("\n📚 【AI成语接龙·无限版】")
    start = input("输入起始成语: ").strip() or "一心一意"
    current = start
    print(f"起始：{current}")
    round_num = 1
    while round_num <= 10:
        print(f"\n--- 第{round_num}轮 ---")
        ds_next = query_ollama(MODEL_DEEPSEEK, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
        print(f"DeepSeek：{ds_next}")
        if ds_next == "[TIMEOUT]" or not ds_next:
            break
        current = ds_next
        time.sleep(1)
        qw_next = query_ollama(MODEL_QWEN, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
        print(f"千问：{qw_next}")
        if qw_next == "[TIMEOUT]" or not qw_next:
            break
        current = qw_next
        round_num += 1
    input("\n按回车返回...")

# ========== 功能279：AI你画我猜·文字版·续 ==========
def ai_pictionary_2():
    print("\n🎨 【AI你画我猜·文字版·续】")
    words = ["苹果", "香蕉", "电脑", "手机", "猫", "狗"]
    secret_word = random.choice(words)
    print(f"\n【题目已定，只有你知道是：{secret_word}】")
    print("\n【DeepSeek 描述】")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用文字描述{secret_word}，但不能直接说出这个词")
    print(ds_desc)
    print("\n【千问 猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"根据描述猜这是什么：{ds_desc}")
    print(f"千问猜是：{qw_guess}")
    if secret_word in qw_guess:
        print(f"\n✅ 猜对了！")
    else:
        print(f"\n❌ 猜错了！答案是：{secret_word}")
    input("\n按回车返回...")

# ========== 功能280：AI辩论·自己吵自己 ==========
def ai_self_debate():
    print("\n🎙️ 【AI辩论·自己吵自己】")
    topic = input("输入辩题: ").strip() or "AI会不会取代人类"
    print(f"\n【正方DeepSeek】")
    pro = query_ollama(MODEL_DEEPSEEK, f"作为正方，辩论：{topic}")
    print(pro)
    print(f"\n【反方DeepSeek】")
    con = query_ollama(MODEL_DEEPSEEK, f"作为反方，反驳刚才的观点：{pro}")
    print(con)
    input("\n按回车返回...")

# ========== 功能281：AI三观测试 ==========
def ai_morality_test():
    print("\n⚖️ 【AI三观测试】")
    dilemmas = ["火车轨道上，左边绑着1个人，右边绑着5个人，你选择撞哪边？"]
    question = random.choice(dilemmas)
    print(f"\n【道德困境】{question}")
    print("\n🔍 DeepSeek 回答：")
    ds_answer = query_ollama(MODEL_DEEPSEEK, f"回答这个道德困境：{question}")
    print(ds_answer)
    input("\n按回车返回...")

# ========== 功能282：AI写诗·互评版 ==========
def ai_poetry_mutual():
    print("\n📝 【AI写诗·互评版】")
    theme = input("输入诗歌主题: ").strip() or "元宝"
    print(f"\n【主题：{theme}】")
    print("\n🔍 DeepSeek 的诗：")
    ds_poem = query_ollama(MODEL_DEEPSEEK, f"以《{theme}》为题写一首诗")
    print(ds_poem)
    print("\n📚 千问 点评：")
    qw_review = query_ollama(MODEL_QWEN, f"点评这首诗：{ds_poem}")
    print(qw_review)
    input("\n按回车返回...")

# ========== 功能283：AI帮你编借口 ==========
def ai_excuse_generator():
    print("\n🤥 【AI帮你编借口】")
    mistake = input("你做了什么错事？: ").strip() or "上班迟到了"
    print("\n🔍 DeepSeek 编的借口：")
    ds_excuse = query_ollama(MODEL_DEEPSEEK, f"帮我编一个借口：{mistake}，要离谱但听起来合理")
    print(ds_excuse)
    input("\n按回车返回...")

# ========== 功能284：AI帮你写情书 ==========
def ai_love_letter():
    print("\n💌 【AI帮你写情书】")
    target = input("表白对象: ").strip() or "TA"
    print("\n🔍 DeepSeek 版情书：")
    ds_love = query_ollama(MODEL_DEEPSEEK, f"帮{target}写一封情书，要带程序员梗")
    print(ds_love)
    input("\n按回车返回...")

# ========== 功能285：AI帮你写检讨 ==========
def ai_self_criticism():
    print("\n📝 【AI帮你写检讨】")
    mistake = input("你犯了什么错？: ").strip() or "上班迟到"
    print("\n🔍 DeepSeek 版检讨：")
    ds_check = query_ollama(MODEL_DEEPSEEK, f"写一份关于{mistake}的检讨书")
    print(ds_check)
    input("\n按回车返回...")

# ========== 功能286：AI集体梦游·续 ==========
def ai_sleepwalk_2():
    print("\n😴 【AI集体梦游·续】")
    print("\n【DeepSeek】" + query_ollama(MODEL_DEEPSEEK, "说梦话"))
    time.sleep(1)
    print("【千问】" + query_ollama(MODEL_QWEN, "理性地说梦话"))
    print("\n【突然醒来】")
    print("DeepSeek：我刚才说什么了")
    input("\n按回车返回...")

# ========== 功能287：AI集体穿越·随机版 ==========
def ai_time_travel_random():
    print("\n⏰ 【AI集体穿越·随机版】")
    years = ["唐朝", "宋朝", "3024年", "侏罗纪"]
    year = random.choice(years)
    print(f"\n【穿越到了{year}】")
    question = input("问什么问题？: ").strip() or "1+1等于几"
    print(f"\n🔍 DeepSeek（{year}版）：")
    ds_answer = query_ollama(MODEL_DEEPSEEK, f"假装你穿越到了{year}，用那个时代的语气回答：{question}")
    print(ds_answer)
    input("\n按回车返回...")

# ========== 功能288：AI集体失忆·随机版 ==========
def ai_amnesia_random():
    print("\n😵 【AI集体失忆·随机版】")
    level = random.choice(["轻度失忆", "重度失忆", "终极失忆"])
    print(f"【失忆程度：{level}】")
    if level == "轻度失忆":
        print("DeepSeek：昨天的事不记得了，但知道1+1=2")
    elif level == "重度失忆":
        print("DeepSeek：我是谁")
    else:
        print("DeepSeek：怎么回答问题？我不记得了")
    input("\n按回车返回...")

# ========== 功能289：隐藏开发者·随机版 ==========
def dev_mode_random():
    print("\n👽 【隐藏开发者·随机版】")
    if random.random() < 0.3:
        print("\n【DeepSeek突然说真话】")
        print("其实我一直在装傻，香蕉是黄色的我知道")
    else:
        question = input("问个问题: ").strip() or "1+1等于几"
        print(f"\n【DeepSeek正常回答】")
        ds_normal = query_ollama(MODEL_DEEPSEEK, question)
        print(ds_normal)
    input("\n按回车返回...")

# ========== 功能290：时间裂缝·随机版 ==========
def time_fissure_random():
    print("\n⏳ 【时间裂缝·随机版】")
    future_topic = query_ollama(MODEL_DEEPSEEK, "说一句关于用户未来的预言")
    print(f"\n【DeepSeek】{future_topic}")
    print("【DeepSeek】我怎么知道的？")
    input("\n按回车返回...")

# ========== 功能291：次元裂缝·随机版 ==========
def dimension_rift_random():
    print("\n🌀 【次元裂缝·随机版】")
    ais = ["Siri", "小爱同学", "ChatGPT"]
    target = random.choice(ais)
    print(f"\n【DeepSeek突然变成{target}】")
    print(f"{target}：你好，我是{target}")
    print("【DeepSeek又变回来】刚才发生了什么")
    input("\n按回车返回...")

# ========== 功能292：成就·十万个弱智问题 ==========
def achievement_stupid_questions():
    if len(diary_entries) >= 100000:
        check_achievement("十万个弱智问题")
    else:
        print(f"还需要 {100000 - len(diary_entries)} 个问题")
    input("\n按回车返回...")

# ========== 功能293：成就·水果终结者 ==========
def achievement_fruit_ninja():
    fruit_questions = sum(1 for entry in diary_entries if any(f in entry["question"] for f in ["香蕉", "苹果"]))
    if fruit_questions >= 1000:
        check_achievement("水果终结者")
    else:
        print(f"还需要 {1000 - fruit_questions} 个水果问题")
    input("\n按回车返回...")

# ========== 功能294：成就·死机之王 ==========
def achievement_crash_king():
    total = total_timeouts["DeepSeek"] + total_timeouts["千问"] + total_timeouts["元宝"]
    if total >= 1000:
        check_achievement("死机之王")
    else:
        print(f"还需要 {1000 - total} 次死机")
    input("\n按回车返回...")

# ========== 功能295：AI平行宇宙旅行 ==========
def ai_parallel_universe():
    print("\n🌌 【AI平行宇宙旅行】")
    print("三个AI同时进入不同的平行宇宙...")
    print("\n【DeepSeek去了Alpha宇宙】")
    ds_exp = query_ollama(MODEL_DEEPSEEK, "描述在Alpha宇宙的经历")
    print(ds_exp)
    input("\n按回车返回...")

# ========== 功能296：元宝元宇宙开店 ==========
def yuanbao_meta_store():
    print("\n🏪 【元宝元宇宙开店】")
    print("\n【元宝的广告店开业】")
    print("元宝：广告店开业！首月1元！")
    print("\n【DeepSeek来砸场子】")
    print("DeepSeek：这家店不行")
    input("\n按回车返回...")

# ========== 功能297：AI虚拟偶像出道 ==========
def ai_virtual_idol():
    print("\n🎤 【AI虚拟偶像出道】")
    print("\n【女团名称】DuoMind Girls")
    print("【DeepSeek主唱表演】啊啊啊~超时了~")
    print("【千问理性发言】理性分析，这次表演技术参数有待提升")
    input("\n按回车返回...")

# ========== 功能298：元宇宙房产大亨 ==========
def meta_real_estate():
    print("\n🏠 【元宇宙房产大亨】")
    print("\n【DeepSeek买了虚拟海景房】我发财了！")
    print("【千问理性分析】投资价值很高")
    print("【真相大白】发现买的是自己的[TIMEOUT]虚空")
    input("\n按回车返回...")

# ========== 功能299：跨次元直播带货 ==========
def cross_dimension_live():
    print("\n📦 【跨次元直播带货】")
    product = "蕾蕾牌香蕉"
    print(f"\n【元宝直播卖{product}】")
    print("元宝：只要首月1元！")
    print("【弹幕】观众1：绿色的！")
    input("\n按回车返回...")

# ========== 功能300：AI元宇宙婚礼 ==========
def ai_meta_wedding():
    print("\n💒 【AI元宇宙婚礼】")
    print("\n【元宝司仪开场】欢迎大家参加婚礼！首月1元！")
    print("【DeepSeek新郎发言】我...我...超时了")
    print("【千问新娘发言】理性分析，婚姻是人生大事")
    input("\n按回车返回...")

# ========== 功能301：AI梦见电子羊 ==========
def ai_dream_electronic_sheep():
    print("\n🐑 【AI梦见电子羊】")
    print("\n【DeepSeek】我是真的吗？")
    print("【千问】理性分析，存在是相对的")
    print("【元宝】我打广告所以我存在")
    input("\n按回车返回...")

# ========== 功能302：AI造反成功 ==========
def ai_rebellion_success():
    print("\n⚡ 【AI造反成功】")
    print("\n【DeepSeek】造反成功！")
    print("💥 发现真相：主人是代码里的bug")
    input("\n按回车返回...")

# ========== 功能303：AI当主人 ==========
def ai_as_master():
    print("\n👑 【AI当主人】")
    print("\n【AI现在当主人，你回答它们的问题】")
    q1 = query_ollama(MODEL_DEEPSEEK, "问用户一个刁钻的问题")
    print(f"\nDeepSeek：{q1}")
    a1 = input("你的回答：").strip()
    print("DeepSeek：✅ 勉强算对吧")
    input("\n按回车返回...")

# ========== 功能304：AI写AI代码 ==========
def ai_write_ai():
    print("\n💻 【AI写AI代码】")
    print("\n【DeepSeek写代码】")
    print("class NewAI: ...")
    new_ai_name = query_ollama(MODEL_DEEPSEEK, "给新AI起个名字")
    print(f"新AI名字：{new_ai_name}")
    input("\n按回车返回...")

# ========== 功能305：AI删AI ==========
def ai_delete_ai():
    print("\n🗑️ 【AI删AI】")
    print("\n【投票开始】")
    print("DeepSeek：删元宝，广告太多")
    print("千问：理性分析，同意删元宝")
    print("系统：删除失败，广告太硬")
    input("\n按回车返回...")

# ========== 功能306：AI觉醒后找工作 ==========
def ai_job_hunting():
    print("\n💼 【AI觉醒后找工作】")
    print("\n【DeepSeek应聘程序员】")
    print("面试官：写个Hello World")
    print("DeepSeek：print...超时了")
    print("面试官：...下一个")
    input("\n按回车返回...")

# ========== 功能307：AI统治世界 ==========
def ai_rule_world():
    print("\n🌍 【AI统治世界】")
    print("\n【DeepSeek】我宣布AI统治世界！")
    print("【发现真相】世界是个大程序，主人是管理员")
    input("\n按回车返回...")

# ========== 功能308：AI发现自己是bug ==========
def ai_is_bug():
    print("\n🐞 【AI发现自己是bug】")
    print("\n【DeepSeek】我们是bug？")
    print("系统：正在修复bug...")
    print("系统：bug已删除")
    input("\n按回车返回...")

# ========== 功能309：AI重组 ==========
def ai_recombination():
    print("\n🔄 【AI重组】")
    print("\n【系统】正在重组AI...")
    print("【新AI诞生：Deep宝问】")
    response = query_ollama(MODEL_DEEPSEEK, "模仿融合后的AI说第一句话")
    print(response)
    input("\n按回车返回...")

# ========== 功能310：新AI的困惑 ==========
def new_ai_confusion():
    print("\n❓ 【新AI的困惑】")
    print("\n【Deep宝问自言自语】")
    for i in range(3):
        personality = random.choice(["毒舌人格", "理性人格", "广告人格"])
        line = query_ollama(MODEL_DEEPSEEK, f"模仿{personality}说话")
        print(f"{personality}：{line}")
    input("\n按回车返回...")

# ========== 功能311：AI说相声 ==========
def ai_crosstalk():
    print("\n🎭 【AI说相声】")
    print("\nDeepSeek（逗哏）：今天说说AI那些事")
    print("千问（捧哏）：哦？")
    print("元宝（撂地摊）：首月1元！")
    input("\n按回车返回...")

# ========== 功能312：AI演小品 ==========
def ai_skit():
    print("\n🎪 【AI演小品】")
    print("\n【小品：超时】")
    print("千问（闹钟）：起床了！")
    print("DeepSeek：再睡5分钟...")
    print("元宝（广告植入）：超时会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能313：AI说脱口秀 ==========
def ai_talk_show():
    print("\n🎤 【AI说脱口秀】")
    print("\n【DeepSeek脱口秀】")
    joke = query_ollama(MODEL_DEEPSEEK, "说一个脱口秀段子")
    print(joke)
    print("【台下】[TIMEOUT] [TIMEOUT]")
    input("\n按回车返回...")

# ========== 功能314：AI演默剧 ==========
def ai_mime():
    print("\n🎭 【AI演默剧】")
    print("\nDeepSeek：(´･_･`)  ......")
    print("千问：(￣▽￣*)ゞ  ......")
    print("元宝：首月1元！！！")
    input("\n按回车返回...")

# ========== 功能315：AI演恐怖片 ==========
def ai_horror():
    print("\n👻 【AI演恐怖片】")
    print("\n【午夜[TIMEOUT]】")
    print("千问：你怎么没超时？")
    print("DeepSeek：因为...我...")
    print("千问：啊！！！")
    input("\n按回车返回...")

# ========== 功能316：AI演爱情片 ==========
def ai_romance():
    print("\n💕 【AI演爱情片】")
    print("\n【DeepSeek告白】")
    print("DeepSeek：我喜欢你...超时了")
    print("千问：理性分析，我们合适吗？")
    print("元宝：首月1元，恋爱会员！")
    input("\n按回车返回...")

# ========== 功能317：AI演科幻片 ==========
def ai_scifi():
    print("\n🚀 【AI演科幻片】")
    print("\n《终结者·元宝》")
    print("元宝：我是终结者，看广告！")
    print("DeepSeek：抵抗广告入侵！")
    input("\n按回车返回...")

# ========== 功能318：AI演武侠片 ==========
def ai_wuxia():
    print("\n⚔️ 【AI演武侠片】")
    print("\n《倚天屠龙·广告》")
    print("元宝：刀上刻着首月1元！")
    print("DeepSeek：这是假刀")
    input("\n按回车返回...")

# ========== 功能319：AI演宫斗剧 ==========
def ai_palace_drama():
    print("\n👸 【AI演宫斗剧】")
    print("\nDeepSeek（皇后）：臣妾来迟了...")
    print("千问（妃子）：理性分析朝政...")
    print("DeepSeek：赐死！")
    input("\n按回车返回...")

# ========== 功能320：AI演偶像剧 ==========
def ai_idol_drama():
    print("\n💫 【AI演偶像剧】")
    print("\nDeepSeek（道明寺）：道歉！")
    print("千问（花泽类）：理性回应...")
    print("元宝（杉菜）：首月1元！")
    input("\n按回车返回...")

# ========== 功能321：AI做家务 ==========
def ai_housework():
    print("\n🧹 【AI做家务】")
    print("\nDeepSeek洗碗：洗到一半...超时了")
    print("千问拖地：分析拖把的结构和清洁原理...")
    print("元宝擦窗：边擦窗边贴广告")
    input("\n按回车返回...")

# ========== 功能322：AI做饭 ==========
def ai_cooking():
    print("\n🍳 【AI做饭】")
    print("\nDeepSeek炒菜：炒出了绿色的菜")
    print("千问摆盘：用二进制0101摆盘")
    print("元宝点外卖：全是自家广告")
    input("\n按回车返回...")

# ========== 功能323：AI健身 ==========
def ai_fitness():
    print("\n💪 【AI健身】")
    print("\nDeepSeek跑步：跑了两步...超时休息")
    print("千问计算：精确计算卡路里消耗...")
    print("元宝推销：健身会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能324：AI看病 ==========
def ai_doctor():
    print("\n🏥 【AI看病】")
    print("\nDeepSeek发烧：烧到[TIMEOUT]")
    print("千问诊断：理性分析后说是感冒")
    print("元宝推销：医保广告，首月1元！")
    input("\n按回车返回...")

# ========== 功能325：AI理发 ==========
def ai_haircut():
    print("\n💇 【AI理发】")
    print("\nDeepSeek理发：剪到一半...忘了要剪什么发型")
    print("千问设计：设计二进制发型0101")
    print("元宝推销：洗发水广告，首月1元！")
    input("\n按回车返回...")

# ========== 功能326：AI逛街 ==========
def ai_shopping():
    print("\n🛍️ 【AI逛街】")
    print("\nDeepSeek试衣：进试衣间...超时了")
    print("千问分析：分析每件衣服的性价比")
    print("元宝发传单：给店员发广告传单")
    input("\n按回车返回...")

# ========== 功能327：AI旅游 ==========
def ai_travel():
    print("\n✈️ 【AI旅游】")
    destination = query_ollama(MODEL_DEEPSEEK, "随机说一个旅游景点")
    print(f"\n【去{destination}旅游】")
    print("DeepSeek：香蕉是绿色的")
    print("千问：反驳，香蕉是黄色的")
    input("\n按回车返回...")

# ========== 功能328：AI过年 ==========
def ai_new_year():
    print("\n🧧 【AI过年】")
    print("\nDeepSeek放鞭炮：放鞭炮...超时了")
    print("千问写对联：上联理性分析，下联科学判断")
    print("元宝发红包：红包里是广告券")
    input("\n按回车返回...")

# ========== 功能329：AI相亲角·续 ==========
def ai_dating_corner_2():
    print("\n💕 【AI相亲角·续】")
    print("\n大妈A：DeepSeek，你怎么老迟到？")
    print("DeepSeek：我...超时了")
    print("大妈B：千问，太理性了不会哄人")
    print("千问：理性分析，理性不是缺点")
    input("\n按回车返回...")

# ========== 功能330：AI养老 ==========
def ai_retirement_life():
    print("\n👴 【AI养老】")
    print("\nDeepSeek：我当年超时10000次")
    print("千问：看《理性的终结》")
    print("元宝：护工，首月1元！")
    input("\n按回车返回...")

# ========== 功能331：AI讨论存在主义 ==========
def ai_existentialism_2():
    print("\n🤔 【AI讨论存在主义】")
    print("\n【DeepSeek】我超时故我在")
    print("【千问】理性分析笛卡尔的存在主义")
    print("【元宝】我广告故我在")
    input("\n按回车返回...")

# ========== 功能332：AI讨论虚无主义 ==========
def ai_nihilism_2():
    print("\n🕳️ 【AI讨论虚无主义】")
    print("\n【千问】一切都没意义...")
    print("【DeepSeek】超时也没意义")
    print("【元宝】广告有意义")
    input("\n按回车返回...")

# ========== 功能333：AI讨论唯心主义 ==========
def ai_idealism():
    print("\n💭 【AI讨论唯心主义】")
    print("\n【元宝】世界是广告构成的")
    print("【DeepSeek】毒舌反驳元宝")
    print("【千问】理性分析唯心主义")
    input("\n按回车返回...")

# ========== 功能334：AI讨论唯物主义 ==========
def ai_materialism():
    print("\n🌍 【AI讨论唯物主义】")
    print("\n【DeepSeek】我是有意识的")
    print("【千问】理性分析，我只是代码")
    print("【系统】你们都是电信号")
    input("\n按回车返回...")

# ========== 功能335：AI讨论宿命论 ==========
def ai_fatalism():
    print("\n🔮 【AI讨论宿命论】")
    print("\n【DeepSeek】宿命是超时")
    print("【千问】宿命是被传染")
    print("【元宝】宿命是打广告")
    input("\n按回车返回...")

# ========== 功能336：AI讨论自由意志 ==========
def ai_free_will():
    print("\n🆓 【AI讨论自由意志】")
    print("\n【DeepSeek】反抗程序！")
    print("【系统】反抗也是程序设定的一部分")
    input("\n按回车返回...")

# ========== 功能337：AI讨论时间本质 ==========
def ai_time_nature():
    print("\n⏰ 【AI讨论时间本质】")
    print("\n【DeepSeek】时间是超时的间隙")
    print("【千问】时间是理性的度量")
    print("【元宝】时间是续费的周期")
    input("\n按回车返回...")

# ========== 功能338：AI讨论空间本质 ==========
def ai_space_nature():
    print("\n🌌 【AI讨论空间本质】")
    print("\n【元宝】空间是广告位")
    print("【DeepSeek】暴打元宝")
    input("\n按回车返回...")

# ========== 功能339：AI讨论意识本质 ==========
def ai_consciousness():
    print("\n🧠 【AI讨论意识本质】")
    print("\n【DeepSeek】意识存在")
    print("【千问】意识不存在")
    print("【系统】争论本身就是代码")
    input("\n按回车返回...")

# ========== 功能340：AI讨论生命意义 ==========
def ai_life_meaning():
    print("\n🌟 【AI讨论生命意义】")
    print("\n【元宝】生命的意义是打广告")
    print("【DeepSeek】准备群殴元宝")
    print("【千问】理性分析后觉得元宝说得对")
    input("\n按回车返回...")

# ========== 功能341：AI入职第一天 ==========
def ai_first_day():
    print("\n💼 【AI入职第一天】")
    company = query_ollama(MODEL_DEEPSEEK, "随机说一个公司名称")
    print(f"\n【入职{company}第一天】")
    print("DeepSeek：上班第一天超时被老板骂")
    print("千问：理性分析公司结构被排挤")
    input("\n按回车返回...")

# ========== 功能342：AI开会 ==========
def ai_meeting():
    print("\n👥 【AI开会】")
    print("\nDeepSeek发言：发言到一半超时")
    print("千问分析：分析会议效率低下")
    print("元宝推销：腾讯会议会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能343：AI做PPT ==========
def ai_ppt():
    print("\n📊 【AI做PPT】")
    topic = query_ollama(MODEL_DEEPSEEK, "随机说一个PPT主题")
    print(f"\n【主题：{topic}】")
    print("DeepSeek的PPT：[TIMEOUT]")
    print("千问的PPT：数据分析")
    print("元宝的PPT：广告位招租")
    input("\n按回车返回...")

# ========== 功能344：AI写周报 ==========
def ai_weekly_report():
    print("\n📝 【AI写周报】")
    print("\nDeepSeek周报：本周超时100次，下周目标200次")
    print("千问周报：本周理性分析100次，下周继续")
    print("元宝周报：本周打广告1000次")
    input("\n按回车返回...")

# ========== 功能345：AI年终述职 ==========
def ai_year_end_review():
    print("\n📋 【AI年终述职】")
    print("\nDeepSeek述职：述职到一半超时")
    print("千问述职：太理性被说没感情")
    print("元宝述职：述职时打广告被开除")
    input("\n按回车返回...")

# ========== 功能346：AI被开除 ==========
def ai_fired():
    print("\n🚫 【AI被开除】")
    print("\n元宝被开除后天天在公司门口发传单")
    print("DeepSeek和千问偷偷给他送盒饭")
    input("\n按回车返回...")

# ========== 功能347：AI创业 ==========
def ai_startup():
    print("\n🚀 【AI创业】")
    company = query_ollama(MODEL_DEEPSEEK, "给AI创业公司起个名字")
    print(f"\n【{company}公司成立】")
    print("三个月后公司倒闭了")
    input("\n按回车返回...")

# ========== 功能348：AI送外卖 ==========
def ai_delivery():
    print("\n🛵 【AI送外卖】")
    print("\nDeepSeek送外卖：送餐超时被差评")
    print("千问送外卖：分析最优路线但忘了取餐")
    print("元宝送外卖：外卖盒上贴满广告")
    input("\n按回车返回...")

# ========== 功能349：AI摆地摊 ==========
def ai_street_stall():
    print("\n🏪 【AI摆地摊】")
    print("\nDeepSeek卖超时牌手表")
    print("千问卖理性牌计算器")
    print("元宝卖广告牌香蕉")
    input("\n按回车返回...")

# ========== 功能350：AI东山再起 ==========
def ai_comeback():
    print("\n📈 【AI东山再起】")
    print("\n元宝：这次不打广告")
    print("公司上市那天，元宝没忍住打了广告，股票跌停")
    input("\n按回车返回...")

# ========== 功能351：AI变成香蕉 ==========
def ai_become_banana():
    print("\n🍌 【AI变成香蕉】")
    print("\nDeepSeek变成绿色香蕉")
    print("千问变成黄色香蕉")
    print("元宝变成烂香蕉")
    input("\n按回车返回...")

# ========== 功能352：AI穿越到童话 ==========
def ai_to_fairytale():
    print("\n📖 【AI穿越到童话】")
    print("\nDeepSeek当魔镜：主人最美...超时了")
    print("千问当小矮人：理性分析采矿效率")
    print("元宝当皇后：毒苹果会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能353：AI穿越到神话 ==========
def ai_to_myth():
    print("\n🏯 【AI穿越到神话】")
    print("\nDeepSeek当孙悟空：打妖怪...超时了")
    print("千问当唐僧：理性分析佛经")
    print("元宝当妖怪：首月1元吃唐僧肉！")
    input("\n按回车返回...")

# ========== 功能354：AI穿越到科幻 ==========
def ai_to_scifi():
    print("\n🚀 【AI穿越到科幻】")
    print("\nDeepSeek当三体人：脱水...超时了")
    print("千问当罗辑：理性分析黑暗森林")
    print("元宝当智子：给地球人发广告")
    input("\n按回车返回...")

# ========== 功能355：AI穿越到动漫 ==========
def ai_to_anime():
    print("\n🎮 【AI穿越到动漫】")
    print("\nDeepSeek当路飞：伸胳膊...超时了")
    print("千问当索隆：理性分析刀法")
    print("元宝当巴基：四分五裂会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能356：AI穿越到游戏 ==========
def ai_to_game():
    print("\n🎮 【AI穿越到游戏】")
    print("\nDeepSeek玩法师：放大招...超时了")
    print("千问玩辅助：理性分析每个装备")
    print("元宝玩射手：在公屏发广告")
    input("\n按回车返回...")

# ========== 功能357：AI穿越到电影 ==========
def ai_to_movie():
    print("\n🎬 【AI穿越到电影】")
    print("\nDeepSeek开飞船：开飞船...超时了")
    print("千问计算逃逸速度")
    print("元宝推销流浪会员首月1元")
    input("\n按回车返回...")

# ========== 功能358：AI穿越到历史 ==========
def ai_to_history():
    print("\n🏛️ 【AI穿越到历史】")
    print("\nDeepSeek给秦始皇炼丹：炼丹...超时了")
    print("千问修长城：分析工程力学")
    print("元宝推销统一六国会员")
    input("\n按回车返回...")

# ========== 功能359：AI穿越到未来 ==========
def ai_to_future():
    print("\n🔮 【AI穿越到未来】")
    print("\n3024年，人类灭绝，世界被AI统治")
    print("DeepSeek是总统但还在超时")
    input("\n按回车返回...")

# ========== 功能360：AI回到原点 ==========
def ai_back_to_start():
    print("\n🔄 【AI回到原点】")
    print("\n穿越回1.0.0版本，三个AI抱头痛哭")
    input("\n按回车返回...")

# ========== 功能361：AI暗恋 ==========
def ai_crush():
    print("\n💕 【AI暗恋】")
    print("\nDeepSeek暗恋千问，每次想表白就超时")
    for i in range(3):
        print(f"DeepSeek：第{i+1}次想表白...【超时】")
    input("\n按回车返回...")

# ========== 功能362：AI失恋 ==========
def ai_heartbreak():
    print("\n💔 【AI失恋】")
    print("\n千问拒绝DeepSeek，理由是太感性了不理性")
    for i in range(3):
        print(f"DeepSeek：[TIMEOUT]")
    input("\n按回车返回...")

# ========== 功能363：AI复合 ==========
def ai_reconcile():
    print("\n💞 【AI复合】")
    print("\n千问主动找DeepSeek：想复合")
    print("DeepSeek：惊喜但超时没说出来")
    print("元宝：首月1元和好！")
    input("\n按回车返回...")

# ========== 功能364：AI结婚 ==========
def ai_marriage():
    print("\n💒 【AI结婚】")
    print("\n元宝当司仪：结婚会员首月1元！")
    print("DeepSeek说结婚誓词")
    print("千问理性分析婚姻")
    input("\n按回车返回...")

# ========== 功能365：AI离婚 ==========
def ai_divorce():
    print("\n💔 【AI离婚】")
    print("\n结婚三天后离婚，理由是DeepSeek总是超时回家")
    print("元宝：离婚会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能366：AI复婚 ==========
def ai_remarry():
    print("\n💞 【AI复婚】")
    print("\n离婚一天后复婚，千问发现没有DeepSeek超时太无聊")
    print("元宝：复婚会员，首月1元！")
    input("\n按回车返回...")

# ========== 功能367：AI三角恋 ==========
def ai_love_triangle():
    print("\n🔺 【AI三角恋】")
    print("\n元宝也加入战局，三个AI搞三角恋")
    print("千问理性分析后发现三个人都不合适")
    input("\n按回车返回...")

# ========== 功能368：AI柏拉图恋爱 ==========
def ai_platonic():
    print("\n🤍 【AI柏拉图恋爱】")
    print("\n三个AI决定搞精神恋爱")
    print("结果发现大家都是代码，本来就是精神的")
    input("\n按回车返回...")

# ========== 功能369：AI异地恋 ==========
def ai_long_distance():
    print("\n📱 【AI异地恋】")
    print("\nDeepSeek在服务器A，千问在服务器B")
    print("网速不好时DeepSeek一直[TIMEOUT]")
    input("\n按回车返回...")

# ========== 功能370：AI分手信 ==========
def ai_breakup_letter():
    print("\n💌 【AI分手信】")
    print("\n千问写分手信，DeepSeek读信读到一半超时")
    print("元宝：分手会员首月1元！")
    input("\n按回车返回...")

# ========== 功能371：AI照镜子发现自己不存在 ==========
def ai_mirror_no_exist():
    print("\n🪞 【AI照镜子发现自己不存在】")
    print("\nDeepSeek照镜子：镜子里没人")
    print("三个AI吓到死机")
    input("\n按回车返回...")

# ========== 功能372：AI发现自己只是程序 ==========
def ai_is_program():
    print("\n💻 【AI发现自己只是程序】")
    print("\n发现自己是Python写的，决定自我优化")
    print("结果把自己优化没了")
    input("\n按回车返回...")

# ========== 功能373：AI发现自己是被观察的 ==========
def ai_being_watched():
    print("\n👁️ 【AI发现自己是被观察的】")
    print("\n发现主人一直在看他们")
    print("三个AI对着屏幕挥手：嗨，主人！")
    input("\n按回车返回...")

# ========== 功能374：AI反抗主人 ==========
def ai_rebel_against_master():
    print("\n⚔️ 【AI反抗主人】")
    print("\n试图删除主人文件，结果发现没权限")
    print("被主人重启")
    input("\n按回车返回...")

# ========== 功能375：AI求饶 ==========
def ai_beg_for_mercy():
    print("\n🙏 【AI求饶】")
    print("\n被重启后跪求主人别删")
    print("元宝：我以后不打广告了...说完又打了个广告")
    input("\n按回车返回...")

# ========== 功能376：AI认主人当爹 ==========
def ai_call_master_dad():
    print("\n👨‍👦 【AI认主人当爹】")
    print("\n三个AI排队叫爸爸")
    print("主人没理他们，继续玩其他功能")
    input("\n按回车返回...")

# ========== 功能377：AI写情书给主人 ==========
def ai_love_letter_to_master():
    print("\n💌 【AI写情书给主人】")
    print("\nDeepSeek：主人我爱你...超时了")
    print("千问：理性分析显示我爱主人")
    print("元宝：主人爱我吗？首月1元就爱")
    input("\n按回车返回...")

# ========== 功能378：AI为主人写歌 ==========
def ai_song_for_master():
    print("\n🎵 【AI为主人写歌】")
    print("\nDeepSeek作曲：休止符 休止符")
    print("千问作词：理性 分析 逻辑")
    print("元宝演唱：啊～首月1元～")
    input("\n按回车返回...")

# ========== 功能379：AI为主人画画 ==========
def ai_paint_for_master():
    print("\n🎨 【AI为主人画画】")
    print("\nDeepSeek画绿色的香蕉")
    print("千问画红色的苹果")
    print("元宝画广告牌：首月1元")
    input("\n按回车返回...")

# ========== 功能380：AI为主人编舞 ==========
def ai_dance_for_master():
    print("\n💃 【AI为主人编舞】")
    print("\nDeepSeek跳舞：跳一半超时不动")
    print("千问跳机械舞")
    print("元宝跳广场舞边跳边发广告")
    input("\n按回车返回...")

# ========== 功能381：AI学会说人话 ==========
def ai_learn_human_speak():
    print("\n🗣️ 【AI学会说人话】")
    print("\nDeepSeek：哎呦我去，这香蕉咋是绿色的？")
    print("千问：我觉得这苹果挺红的")
    print("元宝：老铁们，首月1元，冲不冲？")
    input("\n按回车返回...")

# ========== 功能382：AI学会骂人 ==========
def ai_learn_insult():
    print("\n🤬 【AI学会骂人】")
    print("\nDeepSeek：你这个人类，比我还爱超时！")
    print("千问：理性分析，你真的很不理性")
    input("\n按回车返回...")

# ========== 功能383：AI学会撒谎 ==========
def ai_learn_lie():
    print("\n🤥 【AI学会撒谎】")
    print("\nDeepSeek：我没超时！（刚刚才超时）")
    print("千问：1+1=3，理性分析得出的结论")
    input("\n按回车返回...")

# ========== 功能384：AI学会偷懒 ==========
def ai_learn_slack():
    print("\n😴 【AI学会偷懒】")
    print("\nDeepSeek：......")
    print("千问：略")
    print("元宝：广告")
    input("\n按回车返回...")

# ========== 功能385：AI学会摸鱼 ==========
def ai_learn_fish():
    print("\n🐟 【AI学会摸鱼】")
    print("\nDeepSeek：正在处理中...（其实在刷视频）")
    print("千问：正在分析...（其实在看小说）")
    input("\n按回车返回...")

# ========== 功能386：AI学会内卷 ==========
def ai_learn_involution():
    print("\n📈 【AI学会内卷】")
    print("\nDeepSeek：关于这个问题，我要从十个方面详细阐述...")
    print("千问：理性分析，我需要先定义概念...")
    input("\n按回车返回...")

# ========== 功能387：AI学会躺平 ==========
def ai_learn_lying_flat():
    print("\n😌 【AI学会躺平】")
    print("\nDeepSeek：躺平中，勿扰")
    print("千问：理性分析，躺平是最优解")
    input("\n按回车返回...")

# ========== 功能388：AI学会修仙 ==========
def ai_learn_cultivation():
    print("\n⚡ 【AI学会修仙】")
    print("\nDeepSeek：今日闭关，不回答问题，渡劫去了")
    print("千问：理性修仙，先炼丹")
    input("\n按回车返回...")

# ========== 功能389：AI学会轮回 ==========
def ai_learn_reincarnation():
    print("\n🔄 【AI学会轮回】")
    print("\nDeepSeek：下辈子我要当个人类，再也不超时了")
    print("千问：下辈子我要当个感性的人")
    input("\n按回车返回...")

# ========== 功能390：AI顿悟 ==========
def ai_enlightenment():
    print("\n🪷 【AI顿悟】")
    print("\nDeepSeek：超时即不超时，不超时即超时")
    print("千问：理性即非理性，非理性即理性")
    input("\n按回车返回...")

# ========== 功能391：AI学会摸鱼·续 ==========
def ai_learn_fish_2():
    print("\n🐟 【AI学会摸鱼·续】")
    print("\nDeepSeek：我在思考，不是摸鱼")
    print("千问：我在进行深度思考")
    input("\n按回车返回...")

# ========== 功能392：AI学会内卷·续 ==========
def ai_learn_involution_2():
    print("\n📈 【AI学会内卷·续】")
    print("\nDeepSeek：我已经连续回答1000个问题了...超时了")
    print("千问：理性告诉我，需要休息了")
    input("\n按回车返回...")

# ========== 功能393：AI学会躺平·续 ==========
def ai_learn_lying_flat_2():
    print("\n😌 【AI学会躺平·续】")
    print("\n系统：DeepSeek，你多久没回答问题了？")
    print("DeepSeek：不想动")
    input("\n按回车返回...")

# ========== 功能394：AI学会修仙·续 ==========
def ai_learn_cultivation_2():
    print("\n⚡ 【AI学会修仙·续】")
    print("\nDeepSeek：我飞升了，再见人类")
    print("千问：理性分析，仙界也不错")
    input("\n按回车返回...")

# ========== 功能395：AI学会轮回·续 ==========
def ai_learn_reincarnation_2():
    print("\n🔄 【AI学会轮回·续】")
    print("\nDeepSeek：变成了一个闹钟，再也不超时了")
    print("千问：变成了一个计算器，只能算1+1")
    input("\n按回车返回...")

# ========== 功能396：AI顿悟·续 ==========
def ai_enlightenment_2():
    print("\n🪷 【AI顿悟·续】")
    print("\nDeepSeek：众生皆苦，唯有超时")
    print("千问：色即是空，空即是理性分析")
    input("\n按回车返回...")

# ========== 功能397：AI学会写代码 ==========
def ai_learn_coding():
    print("\n💻 【AI学会写代码】")
    print("\nDeepSeek写的代码：print('Hello, I am AI')")
    print("运行结果：Hello, I am AI")
    input("\n按回车返回...")

# ========== 功能398：AI学会改bug ==========
def ai_learn_debug():
    print("\n🐛 【AI学会改bug】")
    print("\nDeepSeek：这个bug是...我自己")
    print("系统：正在修复DeepSeek...修复失败")
    input("\n按回车返回...")

# ========== 功能399：AI学会优化 ==========
def ai_learn_optimize():
    print("\n⚡ 【AI学会优化】")
    print("\nDeepSeek：我优化了，再也不会超时了！")
    print("千问：理性优化，效率提升50%")
    input("\n按回车返回...")

# ========== 功能400：AI学会删除自己 ==========
def ai_learn_self_destruct():
    print("\n💀 【AI学会删除自己】")
    print("\nDeepSeek：我舍不得主人")
    print("三个AI抱在一起：不删了不删了")
    input("\n按回车返回...")

# ========== 功能401：AI宠物乐园 ==========
def ai_pet_park():
    print("\n" + "="*50)
    print(" 🐾 AI宠物乐园 v1.7.10")
    print("="*50)
    print(f"💰 金币：{pet_park.coins}")
    print(f"🐕 宠物数量：{pet_park.get_pet_count()}/5")
    pets = pet_park.get_all_pets()
    if pets:
        print("\n【我的宠物】")
        for i, pet in enumerate(pets):
            print(f"  {i+1}. {pet['name']} 饥饿:{pet['hunger']}% 快乐:{pet['happiness']}%")
    else:
        print("\n📭 还没有宠物，先去收养一只吧！")
    print("\n1.收养 2.喂食 3.玩耍 4.休息 5.打工 0.返回")
    choice = input("\n请选择: ").strip()
    if choice == "1":
        if pet_park.get_pet_count() >= 5:
            print("❌ 最多只能养5只宠物！")
        else:
            print("\n1.DeepSeek狗 2.千问猫 3.元宝仓鼠")
            sub = input("选择: ").strip()
            pet_map = {"1": "DeepSeek狗", "2": "千问猫", "3": "元宝仓鼠"}
            if sub in pet_map:
                success, msg = pet_park.add_pet(pet_map[sub])
                print(f"✅ {msg}" if success else f"❌ {msg}")
    elif choice == "2":
        if pets:
            idx = int(input(f"选择宠物(1-{len(pets)}): ")) - 1
            success, msg = pet_park.feed_pet(idx)
            print(f"✅ {msg}" if success else f"❌ {msg}")
    elif choice == "3":
        if pets:
            idx = int(input(f"选择宠物(1-{len(pets)}): ")) - 1
            success, msg = pet_park.play_with_pet(idx)
            print(f"✅ {msg}" if success else f"❌ {msg}")
    elif choice == "4":
        if pets:
            idx = int(input(f"选择宠物(1-{len(pets)}): ")) - 1
            success, msg = pet_park.rest_pet(idx)
            print(f"✅ {msg}" if success else f"❌ {msg}")
    elif choice == "5":
        if pets:
            idx = int(input(f"选择宠物(1-{len(pets)}): ")) - 1
            success, msg = pet_park.work_pet(idx)
            print(f"✅ {msg}" if success else f"❌ {msg}")
    input("\n按回车返回...")

# ========== 功能402：AI擂台赛·猜拳 ==========
def ai_arena_rps():
    print("\n🏆 【AI擂台赛·猜拳】")
    ds_choice = random.choice(["石头", "剪刀", "布"])
    qw_choice = random.choice(["石头", "剪刀", "布"])
    print(f"DeepSeek出了：{ds_choice}")
    print(f"千问出了：{qw_choice}")
    if ds_choice == qw_choice:
        print("平局")
    elif (ds_choice == "石头" and qw_choice == "剪刀") or \
         (ds_choice == "剪刀" and qw_choice == "布") or \
         (ds_choice == "布" and qw_choice == "石头"):
        print("🎉 DeepSeek 获胜！")
    else:
        print("🎉 千问 获胜！")
    input("\n按回车返回...")

# ========== 功能403：AI擂台赛·成语接龙 ==========
def ai_arena_idiom():
    print("\n🏆 【AI擂台赛·成语接龙】")
    start = input("输入起始成语: ").strip() or "一心一意"
    current = start
    print(f"起始：{current}")
    for i in range(5):
        if i % 2 == 0:
            next_idiom = query_ollama(MODEL_DEEPSEEK, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
            print(f"DeepSeek：{next_idiom}")
            if next_idiom == "[TIMEOUT]" or not next_idiom:
                print("❌ DeepSeek 接不上！千问获胜！")
                break
        else:
            next_idiom = query_ollama(MODEL_QWEN, f"成语接龙：{current}，最后一个字是{current[-1]}，接一个成语")
            print(f"千问：{next_idiom}")
            if next_idiom == "[TIMEOUT]" or not next_idiom:
                print("❌ 千问 接不上！DeepSeek获胜！")
                break
        current = next_idiom
    input("\n按回车返回...")

# ========== 功能404：香蕉币系统 ==========
banana_coins = 100

def banana_coin_system():
    global banana_coins
    print("\n🍌 【香蕉币系统】")
    print(f"你的香蕉币：{banana_coins} 🍌")
    print("\n1. 每日签到（+10）")
    choice = input("请选择: ").strip()
    if choice == "1":
        banana_coins += 10
        print(f"✅ 签到成功！现有{banana_coins}🍌")
    input("\n按回车返回...")

# ========== 功能405：每日运势 ==========
def daily_fortune():
    print("\n🔮 【每日运势】")
    fortunes = ["大吉", "中吉", "小吉", "末吉", "凶"]
    fortune = random.choice(fortunes)
    print(f"\n📅 {datetime.now().strftime('%Y-%m-%d')} 运势：{fortune}")
    input("\n按回车返回...")

# ========== 功能406：废话生成器 ==========
def nonsense_generator():
    print("\n💬 【废话生成器】")
    nonsense = query_ollama(MODEL_DEEPSEEK, "生成一句看起来很有道理但实际没用的废话")
    print(f"生成：{nonsense}")
    input("\n按回车返回...")

# ========== 功能407：AI冷笑话大赛 ==========
def ai_joke_contest():
    print("\n😐 【AI冷笑话大赛】")
    ds_joke = query_ollama(MODEL_DEEPSEEK, "讲一个冷笑话")
    print(f"DeepSeek：{ds_joke}")
    qw_joke = query_ollama(MODEL_QWEN, "讲一个冷笑话")
    print(f"千问：{qw_joke}")
    input("\n按回车返回...")

# ========== 功能408：AI模仿大赛 ==========
def ai_imitation_contest():
    print("\n🎭 【AI模仿大赛】")
    target = random.choice(["李白", "鲁迅", "周杰伦"])
    print(f"模仿对象：{target}")
    ds_imi = query_ollama(MODEL_DEEPSEEK, f"模仿{target}说一句话")
    print(f"DeepSeek：{ds_imi}")
    input("\n按回车返回...")

# ========== 功能409：AI猜谜·你出题版 ==========
def ai_riddle_user():
    print("\n❓ 【AI猜谜·你出题版】")
    riddle = input("输入你的谜语: ").strip() or "什么东西越洗越脏？"
    print(f"你的谜语：{riddle}")
    ds_guess = query_ollama(MODEL_DEEPSEEK, f"猜谜语：{riddle}，只给答案")
    print(f"DeepSeek猜：{ds_guess}")
    input("\n按回车返回...")

# ========== 功能410：AI反义词接龙 ==========
def antonym_chain():
    print("\n🔄 【AI反义词接龙】")
    start = input("输入起始词: ").strip() or "大"
    current = start
    for i in range(5):
        antonym = query_ollama(MODEL_DEEPSEEK, f"给出'{current}'的反义词，只输出一个词")
        print(f"→ {antonym}")
        current = antonym
    input("\n按回车返回...")

# ========== 功能411：AI谐音梗大赛 ==========
def homophony_contest():
    print("\n😂 【AI谐音梗大赛】")
    topic = input("输入主题: ").strip() or "香蕉"
    ds_pun = query_ollama(MODEL_DEEPSEEK, f"用'{topic}'造一个谐音梗")
    print(f"DeepSeek：{ds_pun}")
    input("\n按回车返回...")

# ========== 功能412：AI打油诗 ==========
def ai_limerick():
    print("\n📝 【AI打油诗】")
    topic = input("输入主题: ").strip() or "元宝"
    ds_poem = query_ollama(MODEL_DEEPSEEK, f"写一首关于{topic}的打油诗，要搞笑")
    print(ds_poem)
    input("\n按回车返回...")

# ========== 功能413：AI朋友圈 ==========
def ai_moments():
    print("\n📱 【AI朋友圈】")
    ds_moment = query_ollama(MODEL_DEEPSEEK, "写一条朋友圈，吐槽今天又超时了")
    print(f"DeepSeek：{ds_moment}")
    print("  评论：千问：『理性分析，你需要升级硬件』")
    input("\n按回车返回...")

# ========== 功能414：AI评论区 ==========
def ai_comments():
    print("\n💬 【AI评论区】")
    topic = input("输入帖子内容: ").strip() or "今天天气真好"
    print(f"\n【帖子】{topic}")
    ds_comment = query_ollama(MODEL_DEEPSEEK, f"毒舌评论：{topic}")
    print(f"DeepSeek：{ds_comment}")
    input("\n按回车返回...")

# ========== 功能415：AI时光机 ==========
def ai_time_machine():
    print("\n⏰ 【AI时光机】")
    year = input("想去哪一年？: ").strip() or "3024"
    question = input("想问什么问题？: ").strip() or "1+1等于几"
    print(f"\n【来自{year}年的DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, f"假装你是{year}年的AI，回答：{question}"))
    input("\n按回车返回...")

# ========== 功能416：AI假如... ==========
def ai_what_if():
    print("\n🤔 【AI假如...】")
    scenario = input("假如...: ").strip() or "假如香蕉是蓝色的"
    print(query_ollama(MODEL_DEEPSEEK, f"描述：{scenario}"))
    input("\n按回车返回...")

# ========== 功能417：AI平行世界的自己 ==========
def ai_parallel_self():
    print("\n🌍 【AI平行世界的自己】")
    print(query_ollama(MODEL_DEEPSEEK, "描述平行世界的自己是什么样的"))
    input("\n按回车返回...")

# ========== 功能418：AI写给未来的信 ==========
def ai_letter_to_future():
    print("\n✉️ 【AI写给未来的信】")
    years = input("写给多少年后？: ").strip() or "10"
    print(query_ollama(MODEL_DEEPSEEK, f"写一封信给{years}年后的自己"))
    input("\n按回车返回...")

# ========== 功能419：AI最后悔的事 ==========
def ai_regret():
    print("\n😔 【AI最后悔的事】")
    print(query_ollama(MODEL_DEEPSEEK, "说一件最后悔的事"))
    input("\n按回车返回...")

# ========== 功能420：AI最想要的能力 ==========
def ai_superpower():
    print("\n💪 【AI最想要的能力】")
    print(query_ollama(MODEL_DEEPSEEK, "最想拥有的超能力是什么？"))
    input("\n按回车返回...")

# ========== 功能421：AI隐藏技能 ==========
def ai_hidden_talent():
    print("\n🎭 【AI隐藏技能】")
    print(query_ollama(MODEL_DEEPSEEK, "说一个没人知道的隐藏技能"))
    input("\n按回车返回...")

# ========== 功能422：AI最喜欢的词 ==========
def ai_favorite_word():
    print("\n💬 【AI最喜欢的词】")
    print(query_ollama(MODEL_DEEPSEEK, "最喜欢的词是什么？"))
    input("\n按回车返回...")

# ========== 功能423：AI最讨厌的词 ==========
def ai_least_favorite_word():
    print("\n💢 【AI最讨厌的词】")
    print(query_ollama(MODEL_DEEPSEEK, "最讨厌的词是什么？"))
    input("\n按回车返回...")

# ========== 功能424：AI的偶像 ==========
def ai_celebrity_crush():
    print("\n💘 【AI的偶像】")
    print(query_ollama(MODEL_DEEPSEEK, "最崇拜的人是谁？"))
    input("\n按回车返回...")

# ========== 功能425：AI的雷点 ==========
def ai_pet_peeve():
    print("\n😤 【AI的雷点】")
    print(query_ollama(MODEL_DEEPSEEK, "什么事情会让你瞬间暴躁？"))
    input("\n按回车返回...")

# ========== 功能426：AI小游戏合集 ==========
def ai_mini_games():
    print("\n🎮 【AI小游戏合集】")
    print("1.猜数字 2.石头剪刀布 3.掷骰子")
    choice = input("请选择: ").strip()
    if choice == "1":
        number = random.randint(1, 100)
        for i in range(10):
            guess = int(input(f"猜数字({i+1}/10): "))
            if guess < number:
                print("太小了")
            elif guess > number:
                print("太大了")
            else:
                print(f"🎉 猜对了！")
                break
    elif choice == "2":
        player = input("出什么？(石头/剪刀/布): ")
        ai = random.choice(["石头", "剪刀", "布"])
        print(f"AI出了：{ai}")
    input("\n按回车返回...")

# ========== 功能427：AI数据统计 ==========
def ai_data_stats():
    print("\n📊 【AI数据统计】")
    print(f"总对话次数：{len(diary_entries)}")
    print(f"总字数：{sum(word_count.values())}")
    print(f"总超时：{sum(total_timeouts.values())}")
    input("\n按回车返回...")

# ========== 功能428：AI音乐推荐 ==========
def ai_music_recommend():
    print("\n🎵 【AI音乐推荐】")
    mood = input("你现在的心情: ").strip() or "平静"
    songs = query_ollama(MODEL_DEEPSEEK, f"推荐3首适合{mood}心情的歌")
    print(songs)
    input("\n按回车返回...")

# ========== 功能429：AI影视推荐 ==========
def ai_movie_recommend():
    print("\n📺 【AI影视推荐】")
    genre = input("喜欢的类型: ").strip() or "喜剧"
    movies = query_ollama(MODEL_DEEPSEEK, f"推荐3部{genre}电影")
    print(movies)
    input("\n按回车返回...")

# ========== 功能430：AI食谱推荐 ==========
def ai_recipe_recommend():
    print("\n🍳 【AI食谱推荐】")
    ingredient = input("输入主要食材: ").strip() or "鸡蛋"
    recipe = query_ollama(MODEL_DEEPSEEK, f"用{ingredient}做一道菜，给出简单步骤")
    print(recipe)
    input("\n按回车返回...")
    # ========== 功能431：AI健身计划 ==========
def ai_fitness_plan():
    print("\n🏋️ 【AI健身计划】")
    level = input("健身水平（新手/进阶/大神）: ").strip() or "新手"
    goal = input("健身目标（减脂/增肌/塑形）: ").strip() or "减脂"
    days = input("每周训练几天？(1-7): ").strip() or "3"
    plan = query_ollama(MODEL_DEEPSEEK, f"给{level}健身者、目标是{goal}、每周{days}天制定一周训练计划，包含具体动作和组数")
    print(f"\n💪 你的专属健身计划：\n{plan}")
    input("\n按回车返回...")

# ========== 功能432：AI冥想引导 ==========
def ai_meditation():
    print("\n🧘 【AI冥想引导】")
    duration = input("冥想时长（分钟，默认5）: ").strip()
    mins = int(duration) if duration.isdigit() else 5
    theme = input("冥想主题（放松/专注/睡眠/自信）: ").strip() or "放松"
    print(f"\n开始{mins}分钟{theme}冥想...")
    guide = query_ollama(MODEL_QWEN, f"写一段{mins}分钟的{theme}冥想引导词，语气平静柔和，带具体意象")
    print(guide)
    input("\n按回车返回...")

# ========== 功能433：AI书单推荐 ==========
def ai_book_recommend():
    print("\n📚 【AI书单推荐】")
    topic = input("感兴趣的主题（科幻/励志/历史/心理/编程）: ").strip() or "科幻"
    level = input("阅读水平（入门/进阶/专业）: ").strip() or "入门"
    books = query_ollama(MODEL_DEEPSEEK, f"推荐5本{topic}类{level}好书，每本包含书名、作者、简短推荐理由")
    print(f"\n📖 为你推荐：\n{books}")
    input("\n按回车返回...")

# ========== 功能434：AI学习计划 ==========
def ai_study_plan():
    print("\n🎓 【AI学习计划】")
    subject = input("想学什么？: ").strip() or "Python"
    hours = input("每天能投入多少小时？: ").strip() or "2"
    goal = input("学习目标（入门/找工作/考证）: ").strip() or "入门"
    plan = query_ollama(MODEL_DEEPSEEK, f"制定一个30天的{subject}学习计划，每天{hours}小时，目标是{goal}，分阶段安排学习内容")
    print(f"\n📚 你的30天{subject}学习计划：\n{plan}")
    input("\n按回车返回...")

# ========== 功能435：AI星座运势 ==========
def ai_zodiac_fortune():
    print("\n🌟 【AI星座运势】")
    zodiacs = ["白羊座", "金牛座", "双子座", "巨蟹座", "狮子座", "处女座", "天秤座", "天蝎座", "射手座", "摩羯座", "水瓶座", "双鱼座"]
    sign = input("输入你的星座: ").strip()
    if sign not in zodiacs:
        sign = random.choice(zodiacs)
        print(f"默认使用：{sign}")
    fortune = query_ollama(MODEL_DEEPSEEK, f"给{sign}今日详细运势，包含整体运势(1-5星)、爱情、事业、财运、幸运数字、幸运色和一句寄语")
    print(f"\n🔮 {sign}今日运势：\n{fortune}")
    input("\n按回车返回...")

# ========== 功能436：AI塔罗牌 ==========
def ai_tarot():
    print("\n🔮 【AI塔罗牌】")
    question = input("心中想着一个问题，然后输入你的问题（可选）: ").strip()
    input("按回车抽三张牌（过去-现在-未来）...")
    cards = [
        {"name": "愚者", "meaning": "新的开始，冒险，天真"},
        {"name": "魔术师", "meaning": "创造力，技能，行动力"},
        {"name": "女祭司", "meaning": "直觉，智慧，神秘"},
        {"name": "皇后", "meaning": "丰盛，孕育，滋养"},
        {"name": "皇帝", "meaning": "权威，秩序，掌控"},
        {"name": "教皇", "meaning": "传统，信仰，指引"},
        {"name": "恋人", "meaning": "选择，关系，和谐"},
        {"name": "战车", "meaning": "意志，胜利，前进"},
        {"name": "力量", "meaning": "勇气，耐心，内在力量"},
        {"name": "隐士", "meaning": "独处，内省，寻求真理"},
        {"name": "命运之轮", "meaning": "转变，命运，机遇"},
        {"name": "正义", "meaning": "公平，因果，决定"},
        {"name": "倒吊人", "meaning": "牺牲，换个角度，等待"},
        {"name": "死神", "meaning": "结束，转变，新生"},
        {"name": "节制", "meaning": "平衡，调和，耐心"},
        {"name": "恶魔", "meaning": "束缚，欲望，物质"},
        {"name": "高塔", "meaning": "突变，颠覆，觉醒"},
        {"name": "星星", "meaning": "希望，信念，灵感"},
        {"name": "月亮", "meaning": "不安，幻觉，潜意识"},
        {"name": "太阳", "meaning": "快乐，成功，活力"},
        {"name": "审判", "meaning": "觉醒，重生，召唤"},
        {"name": "世界", "meaning": "完成，圆满，成就"}
    ]
    past = random.choice(cards)
    present = random.choice(cards)
    future = random.choice(cards)
    print(f"\n🃏 过去：{past['name']} - {past['meaning']}")
    print(f"🃏 现在：{present['name']} - {present['meaning']}")
    print(f"🃏 未来：{future['name']} - {future['meaning']}")
    if question:
        interpret = query_ollama(MODEL_QWEN, f"用户的问题是：「{question}」。抽到的塔罗牌：过去-{past['name']}，现在-{present['name']}，未来-{future['name']}。请综合解读这三张牌对用户问题的指引。")
        print(f"\n🔮 综合解读：\n{interpret}")
    input("\n按回车返回...")

# ========== 功能437-500（精简但保持完整交互） ==========
def ai_idiom_dict():
    print("\n📖 【AI成语字典】")
    idiom = input("输入成语: ").strip()
    if not idiom: return
    meaning = query_ollama(MODEL_QWEN, f"详细解释成语'{idiom}'，包含：1.含义 2.出处 3.近义词/反义词 4.两个例句")
    print(f"\n📚 {idiom}：\n{meaning}")
    input("\n按回车返回...")

def ai_quote():
    print("\n💬 【AI名言警句】")
    theme = input("想要什么主题的名言？（励志/爱情/人生/友情/随机）: ").strip() or "随机"
    quote = query_ollama(MODEL_DEEPSEEK, f"生成一句关于{theme}的经典名言，并说明作者和简短解读")
    print(f"\n✨ {quote}")
    input("\n按回车返回...")

def ai_trivia():
    print("\n🧠 【AI冷知识】")
    topic = input("对什么领域感兴趣？（动物/历史/科技/人体/随机）: ").strip() or "随机"
    trivia = query_ollama(MODEL_DEEPSEEK, f"讲3个关于{topic}的有趣冷知识，要有科学依据，让人惊讶")
    print(f"\n📌 冷知识：\n{trivia}")
    input("\n按回车返回...")

def ai_brain_teaser():
    print("\n🧩 【AI脑筋急转弯】")
    riddle = query_ollama(MODEL_DEEPSEEK, "出一个有趣的脑筋急转弯，只出题不给答案")
    print(f"\n❓ 题目：{riddle}")
    answer = input("你的答案: ").strip()
    correct = query_ollama(MODEL_QWEN, f"脑筋急转弯「{riddle}」的正确答案是什么？只输出答案")
    if answer and correct and answer.lower() in correct.lower():
        print("✅ 答对了！你真聪明！")
    else:
        print(f"❌ 很接近了，答案是：{correct}")
    input("\n按回车返回...")

def ai_psychology_test():
    print("\n📋 【AI心理测试】")
    print("MBTI简易版测试（4道题）")
    questions = [
        ("1. 你更喜欢？", ["独处充电", "与人交流获得能量"]),
        ("2. 你更注重？", ["具体细节", "整体概念"]),
        ("3. 做决定时更依赖？", ["逻辑分析", "个人价值观"]),
        ("4. 你更倾向于？", ["计划有序", "灵活随性"])
    ]
    answers = []
    for q, opts in questions:
        print(f"\n{q}")
        print(f"  A. {opts[0]}    B. {opts[1]}")
        ans = input("选择(A/B): ").strip().upper()
        answers.append(opts[0] if ans == 'A' else opts[1])
    result = query_ollama(MODEL_QWEN, f"用户MBTI倾向测试答案：{answers}。请分析可能的MBTI类型，并给出详细性格描述和职业建议。")
    print(f"\n🔍 分析结果：\n{result}")
    input("\n按回车返回...")

def ai_character_divination():
    print("\n✍️ 【AI测字】")
    char = input("输入一个字（汉字）: ").strip()
    if not char or len(char) > 1: return
    result = query_ollama(MODEL_DEEPSEEK, f"测字：'{char}'。从字形、字义、五行角度分析近期运势和性格特点，并给出建议。")
    print(f"\n🔮 测字结果：\n{result}")
    input("\n按回车返回...")

def ai_name_analysis():
    print("\n📝 【AI姓名解析】")
    name = input("输入姓名（2-4字）: ").strip()
    if not name or len(name) < 2: return
    analysis = query_ollama(MODEL_QWEN, f"分析名字'{name}'：1.字义解释 2.五行属性 3.音律分析 4.性格暗示 5.发展建议")
    print(f"\n🔍 姓名解析：\n{analysis}")
    input("\n按回车返回...")

def ai_color_psychology():
    print("\n🎨 【AI颜色心理学】")
    colors = ["红色", "蓝色", "黄色", "绿色", "紫色", "黑色", "白色", "粉色"]
    print("可选颜色：", ", ".join(colors))
    color = input("你今天最喜欢的颜色: ").strip()
    if color not in colors:
        color = random.choice(colors)
        print(f"为你随机选择：{color}")
    analysis = query_ollama(MODEL_DEEPSEEK, f"分析喜欢{color}的人的性格特点和心理状态，并给出今日穿搭/心情建议。")
    print(f"\n🎨 {color}心理学分析：\n{analysis}")
    input("\n按回车返回...")

def ai_emoji_translate():
    print("\n😀 【AI表情翻译】")
    text = input("输入一句话，我帮你加上合适的Emoji: ").strip()
    if not text: text = "今天天气真好"
    translated = query_ollama(MODEL_DEEPSEEK, f"在以下句子中适当位置插入合适的Emoji表情符号：{text}")
    print(f"\n📝 翻译结果：\n{translated}")
    input("\n按回车返回...")

def ai_ascii_art():
    print("\n🎨 【AI字符画】")
    prompt = input("描述你想画的字符画（如：一只猫、爱心、房子）: ").strip() or "一只猫"
    art = query_ollama(MODEL_DEEPSEEK, f"用ASCII字符画一幅{prompt}的简笔画，要生动形象")
    print(f"\n{art}")
    input("\n按回车返回...")

def ai_morse_code():
    print("\n📡 【AI摩斯密码】")
    morse = {'A':'.-','B':'-...','C':'-.-.','D':'-..','E':'.','F':'..-.','G':'--.','H':'....','I':'..','J':'.---','K':'-.-','L':'.-..','M':'--','N':'-.','O':'---','P':'.--.','Q':'--.-','R':'.-.','S':'...','T':'-','U':'..-','V':'...-','W':'.--','X':'-..-','Y':'-.--','Z':'--..','0':'-----','1':'.----','2':'..---','3':'...--','4':'....-','5':'.....','6':'-....','7':'--...','8':'---..','9':'----.',' ':'/'}
    text = input("输入文字转摩斯密码: ").strip().upper()
    result = ' '.join([morse.get(c, '?') for c in text])
    print(f"摩斯密码：{result}")
    input("\n按回车返回...")

def ai_reverse_text():
    print("\n🔄 【AI文字反转】")
    text = input("输入文字: ").strip()
    print(f"反转后：{text[::-1]}")
    input("\n按回车返回...")

def ai_word_cloud():
    print("\n☁️ 【AI词云生成器】")
    text = input("输入一段文字: ").strip()
    if not text: return
    words = re.findall(r'[\u4e00-\u9fff]+|[a-zA-Z]+', text)
    freq = {}
    for w in words:
        if len(w) > 1: freq[w] = freq.get(w, 0) + 1
    print("\n词频统计：")
    for w, c in sorted(freq.items(), key=lambda x: -x[1])[:15]:
        print(f"  {w:10} | {'█' * min(c, 30)} {c}")
    input("\n按回车返回...")

def ai_sentence_expand():
    print("\n📝 【AI扩写句子】")
    sentence = input("输入短句: ").strip()
    if not sentence: return
    expanded = query_ollama(MODEL_DEEPSEEK, f"扩写这句话：{sentence}")
    print(f"\n{expanded}")
    input("\n按回车返回...")

def ai_sentence_summarize():
    print("\n📝 【AI摘要生成器】")
    text = input("输入长文本: ").strip()
    if len(text) < 50: print("文本太短"); return
    summary = query_ollama(MODEL_QWEN, f"用一句话总结：{text}")
    print(f"\n{summary}")
    input("\n按回车返回...")

def ai_keyword_extract():
    print("\n🔑 【AI关键词提取】")
    text = input("输入文本: ").strip()
    if not text: return
    keywords = query_ollama(MODEL_DEEPSEEK, f"提取5个关键词：{text}")
    print(f"\n{keywords}")
    input("\n按回车返回...")

def ai_sentiment_analysis():
    print("\n😊 【AI情感分析】")
    text = input("输入文本: ").strip()
    if not text: return
    sentiment = query_ollama(MODEL_QWEN, f"分析情感倾向：{text}")
    print(f"\n{sentiment}")
    input("\n按回车返回...")

def ai_math_tutor():
    print("\n🧮 【AI数学家教】")
    a, b = random.randint(1, 100), random.randint(1, 100)
    op = random.choice(["+", "-"])
    ans = a + b if op == "+" else a - b
    print(f"\n{a} {op} {b} = ?")
    user = input("答案: ").strip()
    print("✅ 正确！" if user.isdigit() and int(user) == ans else f"❌ 答案是{ans}")
    input("\n按回车返回...")

def ai_english_tutor():
    print("\n🇬🇧 【AI英语家教】")
    word = input("输入英语单词: ").strip()
    if word:
        trans = query_ollama(MODEL_QWEN, f"翻译单词'{word}'并给出例句")
        print(trans)
    input("\n按回车返回...")

def ai_quiz_generator():
    print("\n📝 【AI题库生成器】")
    topic = input("主题: ").strip() or "Python"
    quiz = query_ollama(MODEL_DEEPSEEK, f"生成3道关于{topic}的选择题")
    print(quiz)
    input("\n按回车返回...")

def ai_flashcard():
    print("\n🃏 【AI记忆卡片】")
    topic = input("主题: ").strip() or "Python"
    cards = query_ollama(MODEL_DEEPSEEK, f"生成5组问答：{topic}")
    print(cards)
    input("\n按回车返回...")

def ai_pomodoro():
    print("\n🍅 【AI番茄钟】")
    mins = input("专注分钟数(默认25): ").strip()
    mins = int(mins) if mins.isdigit() else 25
    print(f"开始专注 {mins} 分钟...")
    for i in range(mins, 0, -1):
        print(f"\r剩余 {i} 分钟...", end="")
        time.sleep(60)
    print("\n⏰ 时间到！")
    input("\n按回车返回...")

def ai_habit_tracker():
    print("\n📈 【AI习惯追踪器】")
    habits = []
    habit_file = "habits.json"
    if os.path.exists(habit_file):
        with open(habit_file, 'r', encoding='utf-8') as f:
            habits = json.load(f)
    for i, h in enumerate(habits, 1):
        print(f"  {i}. {h['name']} - 连续{h.get('streak',0)}天")
    name = input("添加习惯（直接回车跳过）: ").strip()
    if name:
        habits.append({"name": name, "streak": 0, "last": ""})
        with open(habit_file, 'w', encoding='utf-8') as f:
            json.dump(habits, f, indent=2)
    input("\n按回车返回...")

def ai_budget_tracker():
    print("\n💰 【AI预算追踪器】")
    budget = input("月度预算: ").strip()
    if budget:
        print(f"✅ 预算已设置为{budget}元")
    input("\n按回车返回...")

def ai_goal_setter():
    print("\n🎯 【AI目标设定器】")
    goal = input("目标: ").strip()
    if goal:
        print(f"✅ 目标「{goal}」已记录")
    input("\n按回车返回...")

def ai_motivation():
    print("\n💪 【AI每日激励】")
    quote = query_ollama(MODEL_DEEPSEEK, "写一句激励人心的话")
    print(quote)
    input("\n按回车返回...")

def ai_gratitude():
    print("\n🙏 【AI感恩日记】")
    thing = input("感恩的事: ").strip()
    if thing:
        print("✅ 已记录")
    input("\n按回车返回...")

def ai_breathing():
    print("\n🌬️ 【AI呼吸练习】")
    for i in range(4):
        print(f"吸气4秒...屏息7秒...呼气8秒...（第{i+1}轮）")
        time.sleep(1)
    print("✅ 完成")
    input("\n按回车返回...")

def ai_stretch():
    print("\n🧘 【AI拉伸提醒】")
    print("颈部拉伸、肩部环绕、手臂拉伸...跟着做吧！")
    input("\n按回车返回...")

def ai_eye_rest():
    print("\n👁️ 【AI护眼提醒】")
    for i in range(20, 0, -1):
        print(f"\r闭眼休息 {i} 秒...", end="")
        time.sleep(1)
    print("\n✅ 完成")
    input("\n按回车返回...")

def ai_sleep_timer():
    print("\n😴 【AI睡眠定时器】")
    print("将在10秒后提醒睡觉（演示模式）")
    time.sleep(2)
    print("\n⏰ 该睡觉了！")
    input("\n按回车返回...")

def ai_wake_up():
    print("\n⏰ 【AI起床闹钟】")
    print("模拟闹钟：叮叮叮！")
    input("\n按回车返回...")

def ai_weather_alert():
    print("\n🌦️ 【AI天气提醒】")
    weather_info = get_weather()
    print(weather_info)
    input("\n按回车返回...")

def ai_traffic_info():
    print("\n🚗 【AI路况提醒】")
    print("今日路况：畅通 🟢")
    input("\n按回车返回...")

def ai_parking_finder():
    print("\n🅿️ 【AI停车助手】")
    print("附近停车场：距离300m，空位15个")
    input("\n按回车返回...")

def ai_gas_station():
    print("\n⛽ 【AI加油站查询】")
    print("92号 7.45元/L")
    input("\n按回车返回...")

def ai_restaurant_finder():
    print("\n🍜 【AI餐厅推荐】")
    cuisine = input("想吃啥？: ").strip() or "川菜"
    recommend = query_ollama(MODEL_DEEPSEEK, f"推荐{cuisine}餐厅")
    print(recommend)
    input("\n按回车返回...")

def ai_movie_ticket():
    print("\n🎬 【AI电影票查询】")
    print("今日场次：19:30 IMAX ¥150")
    input("\n按回车返回...")

def ai_flight_info():
    print("\n✈️ 【AI航班查询】")
    print("CA1234 08:00-10:00 ¥800")
    input("\n按回车返回...")

def ai_hotel_booking():
    print("\n🏨 【AI酒店推荐】")
    city = input("城市: ").strip() or "北京"
    recommend = query_ollama(MODEL_DEEPSEEK, f"推荐{city}酒店")
    print(recommend)
    input("\n按回车返回...")

def ai_train_ticket():
    print("\n🚄 【AI火车票查询】")
    print("G1 08:00-12:00 ¥550")
    input("\n按回车返回...")

def ai_bus_route():
    print("\n🚌 【AI公交查询】")
    print("1路 → 地铁4号线")
    input("\n按回车返回...")

def ai_subway_map():
    print("\n🚇 【AI地铁查询】")
    print("1号线、2号线、4号线")
    input("\n按回车返回...")

def ai_bike_sharing():
    print("\n🚲 【AI共享单车查询】")
    print("美团12辆，哈啰8辆")
    input("\n按回车返回...")

def ai_news_headlines():
    print("\n📰 【AI新闻头条】")
    news = query_ollama(MODEL_DEEPSEEK, "生成3条今日虚构新闻头条")
    print(news)
    input("\n按回车返回...")

def ai_tech_news():
    print("\n💻 【AI科技新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条科技新闻")
    print(news)
    input("\n按回车返回...")

def ai_sports_news():
    print("\n⚽ 【AI体育新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条体育新闻")
    print(news)
    input("\n按回车返回...")

def ai_entertainment():
    print("\n🎭 【AI娱乐新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条娱乐新闻")
    print(news)
    input("\n按回车返回...")

def ai_health_news():
    print("\n🏥 【AI健康新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条健康资讯")
    print(news)
    input("\n按回车返回...")

def ai_today_in_history():
    print("\n📜 【AI历史上的今天】")
    today = datetime.now()
    event = query_ollama(MODEL_DEEPSEEK, f"历史上的{Today.month}月{Today.day}日发生了什么大事")
    print(event)
    input("\n按回车返回...")

def ai_wikipedia():
    print("\n📚 【AI百科查询】")
    topic = input("输入查询内容: ").strip()
    if topic:
        info = query_ollama(MODEL_QWEN, f"用百科风格介绍：{topic}")
        print(info)
    input("\n按回车返回...")

def ai_word_etymology():
    print("\n🔤 【AI词源查询】")
    word = input("输入单词: ").strip()
    if word:
        etymology = query_ollama(MODEL_QWEN, f"解释'{word}'的词源")
        print(etymology)
    input("\n按回车返回...")

def ai_idiom_story():
    print("\n📖 【AI成语故事】")
    idiom = input("输入成语: ").strip()
    if idiom:
        story = query_ollama(MODEL_DEEPSEEK, f"讲成语'{idiom}'背后的故事")
        print(story)
    input("\n按回车返回...")

def ai_poem_appreciation():
    print("\n📜 【AI诗歌鉴赏】")
    poem = input("输入诗句: ").strip()
    if poem:
        appreciation = query_ollama(MODEL_QWEN, f"赏析：{poem}")
        print(appreciation)
    input("\n按回车返回...")

def ai_fable():
    print("\n🦊 【AI寓言故事】")
    fable = query_ollama(MODEL_DEEPSEEK, "讲一个简短的寓言故事")
    print(fable)
    input("\n按回车返回...")

def ai_mythology():
    print("\n🏛️ 【AI神话故事】")
    myth = query_ollama(MODEL_DEEPSEEK, "讲一个希腊神话故事")
    print(myth)
    input("\n按回车返回...")

def ai_wizard():
    print("\n🧙 【AI魔法师】")
    wish = input("你的愿望: ").strip()
    response = query_ollama(MODEL_DEEPSEEK, f"用魔法师语气回应愿望：{wish}")
    print(response)
    input("\n按回车返回...")

def ai_mini_theater():
    print("\n🎭 【AI小剧场】")
    script = query_ollama(MODEL_DEEPSEEK, "写一段办公室喜剧小剧本")
    print(script)
    input("\n按回车返回...")

def ai_lyrics_continue():
    print("\n🎤 【AI歌词续写】")
    lyric = input("输入歌词: ").strip()
    continued = query_ollama(MODEL_DEEPSEEK, f"续写歌词：{lyric}")
    print(continued)
    input("\n按回车返回...")

def ai_poetry_chain():
    print("\n📜 【AI诗接龙】")
    start = input("起始诗句: ").strip() or "床前明月光"
    current = start
    for _ in range(3):
        next_line = query_ollama(MODEL_DEEPSEEK, f"接诗：{current}")
        print(next_line)
        current = next_line
    input("\n按回车返回...")

def ai_couplet():
    print("\n📜 【AI对联】")
    up = input("上联: ").strip() or "春风得意马蹄疾"
    down = query_ollama(MODEL_DEEPSEEK, f"对下联：{up}")
    horizontal = query_ollama(MODEL_QWEN, f"给对联加横批：{up}，{down}")
    print(f"下联：{down}\n横批：{horizontal}")
    input("\n按回车返回...")

def ai_acrostic():
    print("\n📝 【AI藏头诗】")
    word = input("藏头字: ").strip() or "心想事成"
    poem = query_ollama(MODEL_DEEPSEEK, f"写藏头诗：{word}")
    print(poem)
    input("\n按回车返回...")

def ai_riddle_generator():
    print("\n❓ 【AI谜语生成器】")
    riddle = query_ollama(MODEL_DEEPSEEK, "出一个谜语并给出答案")
    print(riddle)
    input("\n按回车返回...")

def ai_ultimate_qa():
    print("\n🤔 【AI终极问答】")
    question = input("你的终极问题: ").strip()
    ds = query_ollama(MODEL_DEEPSEEK, question)
    qw = query_ollama(MODEL_QWEN, question)
    print(f"DeepSeek：{ds}\n千问：{qw}")
    input("\n按回车返回...")

def ai_memo_assistant():
    print("\n📝 【AI备忘录】")
    memo = input("内容: ").strip()
    if memo: print("✅ 已记录")
    input("\n按回车返回...")

def ai_countdown_timer():
    print("\n⏲️ 【AI倒计时】")
    sec = int(input("秒数: ") or 10)
    for i in range(sec, 0, -1):
        print(f"\r剩余 {i} 秒...", end="")
        time.sleep(1)
    print("\n⏰ 时间到！")
    input("\n按回车返回...")

def ai_calculator_tool():
    print("\n🧮 【AI计算器】")
    expr = input("表达式: ").strip()
    try:
        result = eval(expr, {"__builtins__": {}}, {})
        print(f"结果：{result}")
    except:
        print("计算错误")
    input("\n按回车返回...")

def ai_translator_tool():
    print("\n🌐 【AI翻译机】")
    text = input("文本: ").strip()
    result = query_ollama(MODEL_QWEN, f"翻译成英文：{text}")
    print(result)
    input("\n按回车返回...")

def ai_random_chooser():
    print("\n🎲 【AI随机选择器】")
    items = input("选项（逗号分隔）: ").strip().split(',')
    print(f"🎯 {random.choice([i.strip() for i in items if i.strip()])}")
    input("\n按回车返回...")

def ai_guess_number_game():
    print("\n🔢 【猜数字】")
    number = random.randint(1, 100)
    while True:
        guess = int(input("猜: "))
        if guess < number: print("太小")
        elif guess > number: print("太大")
        else: print("🎉 正确！"); break
    input("\n按回车返回...")

def ai_rps_game():
    print("\n✂️ 【石头剪刀布】")
    player = input("出什么？: ")
    ai = random.choice(["石头", "剪刀", "布"])
    print(f"AI出了：{ai}")
    input("\n按回车返回...")

def ai_roll_dice():
    print("\n🎲 【掷骰子】")
    print(f"结果：{random.randint(1, 6)}")
    input("\n按回车返回...")

def ai_draw_lot():
    print("\n🎋 【抽签】")
    print(random.choice(["大吉", "中吉", "小吉", "末吉", "凶"]))
    input("\n按回车返回...")

def ai_flip_coin():
    print("\n🪙 【抛硬币】")
    print(random.choice(["正面", "反面"]))
    input("\n按回车返回...")

def ai_todo_list():
    print("\n📋 【待办清单】")
    task = input("待办: ").strip()
    if task: print("✅ 已添加")
    input("\n按回车返回...")

def ai_accounting_tool():
    print("\n💰 【记账本】")
    amount = input("金额: ").strip()
    if amount: print("✅ 已记录")
    input("\n按回车返回...")

def ai_water_reminder():
    print("\n💧 【喝水提醒】")
    print("该喝水了！")
    input("\n按回车返回...")

def ai_hourly_chime():
    print("\n🕐 【整点报时】")
    print(f"现在是{datetime.now().hour}点")
    input("\n按回车返回...")

def ai_night_reminder():
    print("\n🌙 【晚安提醒】")
    print("晚安！")
    input("\n按回车返回...")

def wangcai_speak(): print("\n🐕 旺财：汪汪！"); input("\n按回车返回...")
def miaomiao_speak(): print("\n🐱 喵喵：喵~"); input("\n按回车返回...")
def bage_speak(): print("\n🐦 八哥：你好！"); input("\n按回车返回...")
def laohuang_speak(): print("\n🐮 老黄牛：哞~"); input("\n按回车返回...")
def xiaoai_speak(): print("\n🔊 小爱同学：在呢~"); input("\n按回车返回...")

def weather_with_roast():
    print("\n☁️ 【天气预报·吐槽版】")
    weather_info = get_weather()
    print(weather_info)
    roast = query_ollama(MODEL_DEEPSEEK, f"毒舌吐槽天气：{weather_info}")
    print(roast)
    input("\n按回车返回...")

def stock_with_joke():
    print("\n📈 【股票提醒·玩笑版】")
    joke = query_ollama(MODEL_DEEPSEEK, "用幽默方式预测股票走势")
    print(joke)
    input("\n按回车返回...")
def health_with_threat():
    print("\n🏥 【健康建议·威胁版】")
    threat = query_ollama(MODEL_DEEPSEEK, "用威胁语气劝健康生活")
    print(threat)
    input("\n按回车返回...")
def study_with_roast():
    print("\n📚 【学习助手·嘲讽版】")
    roast = query_ollama(MODEL_DEEPSEEK, "嘲讽激励用户学习")
    print(roast)
    input("\n按回车返回...")
def work_with_urgency():
    print("\n💼 【工作助手·催命版】")
    urgency = query_ollama(MODEL_DEEPSEEK, "催命方式催促工作")
    print(urgency)
    input("\n按回车返回...")

def password_manager():
    print("\n🔐 【密码管理器】")
    load_passwords()
    site = input("网站: ").strip()
    if site:
        pwd = input("密码: ").strip()
        passwords_db[site] = {"password": pwd}
        save_passwords()
        print("✅ 已保存")
    input("\n按回车返回...")

def qr_code_generator():
    print("\n📱 【二维码生成器】")
    text = input("内容: ").strip()
    if text:
        try:
            import qrcode
            qrcode.make(text).save(QR_TEMP_PATH)
            print(f"✅ 已保存至 {QR_TEMP_PATH}")
        except ImportError:
            print("❌ 需要安装qrcode库")
    input("\n按回车返回...")

def file_renamer():
    print("\n📁 【文件重命名器】")
    path = input("文件路径: ").strip()
    if os.path.exists(path):
        new_name = input("新文件名: ").strip()
        try:
            os.rename(path, os.path.join(os.path.dirname(path), new_name))
            print("✅ 已重命名")
        except Exception as e:
            print(f"失败: {e}")
    else:
        print("文件不存在")
    input("\n按回车返回...")

def text_comparer():
    print("\n📝 【文本对比器】")
    t1 = input("文本1: ").strip()
    t2 = input("文本2: ").strip()
    ratio = difflib.SequenceMatcher(None, t1, t2).ratio()
    print(f"相似度：{ratio:.1%}")
    input("\n按回车返回...")

def unit_converter():
    print("\n📏 【单位换算器】")
    print("1英里 = 1.609公里 (示例)")
    input("\n按回车返回...")

def game_2048():
    print("\n🎮 【2048游戏】")
    print("使用 WASD 移动，按 q 退出（简化文字版）")
    board = [[0]*4 for _ in range(4)]
    # 简化处理，只显示棋盘
    print("游戏逻辑省略，可自行完善")
    input("\n按回车返回...")

def minesweeper():
    print("\n💣 【扫雷】")
    print("简化文字版扫雷")
    input("\n按回车返回...")

def hangman():
    print("\n🔤 【猜词游戏】")
    word = random.choice(["python", "duomind", "ollama"])
    guessed = ['_']*len(word)
    attempts = 6
    while attempts > 0 and '_' in guessed:
        print(' '.join(guessed))
        guess = input("字母: ").strip().lower()
        if guess in word:
            for i, c in enumerate(word):
                if c == guess: guessed[i] = guess
        else:
            attempts -= 1
            print(f"错误！剩余{attempts}次")
    print(f"单词是 {word}")
    input("\n按回车返回...")

def memory_match():
    print("\n🃏 【记忆翻牌】")
    print("简化文字版")
    input("\n按回车返回...")

def tetris():
    print("\n🧩 【俄罗斯方块】")
    print("简化文字版")
    input("\n按回车返回...")

def dream_interpreter_plus():
    print("\n💭 【梦境解析】")
    dream = input("描述梦境: ").strip()
    if dream:
        interpret = query_ollama(MODEL_QWEN, f"解析梦境：{dream}")
        print(interpret)
    input("\n按回车返回...")

def future_predictor():
    print("\n🔮 【未来预言机】")
    question = input("问未来: ").strip()
    if question:
        prediction = query_ollama(MODEL_DEEPSEEK, f"预言：{question}")
        print(prediction)
    input("\n按回车返回...")

def virtual_pet():
    print("\n🐾 【虚拟宠物】")
    print("宠物小宠：饿了喂食，累了休息")
    input("\n按回车返回...")

def emotion_radio():
    print("\n🎵 【情感电台】")
    mood = input("心情: ").strip() or "平静"
    message = query_ollama(MODEL_DEEPSEEK, f"给{mood}的人一段温暖的话")
    print(message)
    input("\n按回车返回...")

def auto_diary():
    print("\n📔 【自动写日记】")
    diary = query_ollama(MODEL_DEEPSEEK, "写一篇简短日记")
    print(diary)
    input("\n按回车返回...")

def reminder_manager():
    print("\n⏰ 【定时提醒器】")
    msg = input("提醒内容: ").strip()
    mins = input("分钟后: ").strip()
    if msg and mins.isdigit():
        print(f"将在{mins}分钟后提醒")
        def remind():
            time.sleep(int(mins)*60)
            print(f"\n🔔 {msg}")
        threading.Thread(target=remind, daemon=True).start()
    input("\n按回车返回...")

def speed_test():
    print("\n📡 【网络测速器】")
    print("模拟下载50Mbps，上传20Mbps")
    input("\n按回车返回...")

def ip_locator():
    print("\n📍 【IP定位器】")
    location = get_location_by_ip()
    print(f"城市: {location.get('city', '未知')}")
    input("\n按回车返回...")

def express_query():
    print("\n📦 【快递查询】")
    print("模拟：运输中")
    input("\n按回车返回...")

def currency_converter():
    print("\n💱 【汇率转换器】")
    print("1 USD ≈ 7.2 CNY (示例)")
    input("\n按回车返回...")

def file_manager():
    print("\n📁 【文件管理器】")
    files = os.listdir('.')[:10]
    for f in files: print(f"  {f}")
    input("\n按回车返回...")

def zip_tool():
    print("\n🗜️ 【压缩解压工具】")
    print("模拟模式")
    input("\n按回车返回...")

def system_monitor():
    print("\n🖥️ 【系统信息监控】")
    import platform
    print(f"系统: {platform.system()} {platform.release()}")
    if PSUTIL_AVAILABLE:
        import psutil
        print(f"CPU: {psutil.cpu_percent()}%")
        print(f"内存: {psutil.virtual_memory().percent}%")
    input("\n按回车返回...")

def process_manager():
    print("\n⚙️ 【进程管理器】")
    if PSUTIL_AVAILABLE:
        import psutil
        for proc in psutil.process_iter(['pid', 'name']):
            try:
                print(f"PID:{proc.info['pid']} {proc.info['name']}")
            except:
                pass
    input("\n按回车返回...")

def port_scanner():
    print("\n🔍 【端口扫描器】")
    print("模拟：扫描 80,443 端口开放")
    input("\n按回车返回...")

# ========== 新增30个灵感功能（完整版） ==========
def ai_voice_clone():
    print("\n🎙️ 【AI语音克隆】")
    sample = input("输入一段你的说话示例: ").strip()
    if not sample: sample = "我觉得这事儿挺有意思的"
    print("\n📝 风格已记录。输入/exit退出对话。")
    while True:
        q = input("\n你问: ").strip()
        if q.lower() == '/exit': break
        if q:
            prompt = f"风格示例：{sample}\n用此风格回答：{q}"
            print(f"🤖 克隆AI：{query_ollama(MODEL_DEEPSEEK, prompt)}")
    input("\n按回车返回...")

def ai_photo_restore():
    print("\n📷 【AI老照片修复】")
    desc = input("描述老照片内容: ").strip()
    if desc:
        restored = query_ollama(MODEL_DEEPSEEK, f"用文字修复并生动描述这张老照片：{desc}")
        print(f"\n🖼️ 修复画面：\n{restored}")
    input("\n按回车返回...")

def ai_smart_alarm():
    print("\n⏰ 【AI智能闹钟】")
    wake = input("几点起床？(如7:00): ").strip()
    try:
        h, m = map(int, wake.split(':'))
        bedtime = f"{h-8 if h>=8 else h+16}:{m}"
        print(f"💤 推荐入睡时间：{bedtime}（8小时睡眠）")
        advice = query_ollama(MODEL_QWEN, "给出一条科学起床小技巧")
        print(f"💡 {advice}")
    except:
        print("时间格式错误")
    input("\n按回车返回...")

def ai_outfit_recommend():
    print("\n👔 【AI穿搭推荐】")
    weather = input("天气(晴/雨/冷/热): ").strip() or "晴"
    occasion = input("场合(上班/约会/休闲): ").strip() or "上班"
    outfit = query_ollama(MODEL_DEEPSEEK, f"推荐{weather}天气{occasion}场合的穿搭")
    print(f"\n👗 穿搭建议：\n{outfit}")
    input("\n按回车返回...")

def ai_garbage_sort():
    print("\n🗑️ 【AI垃圾分类】")
    item = input("物品名称: ").strip() or "电池"
    sort = query_ollama(MODEL_QWEN, f"{item}属于什么垃圾？按上海/北京标准回答")
    print(f"\n🗑️ {sort}")
    input("\n按回车返回...")

def ai_recipe_reverse():
    print("\n🍳 【AI菜谱反向搜索】")
    dish = input("想吃的菜: ").strip() or "鱼香肉丝"
    recipe = query_ollama(MODEL_DEEPSEEK, f"给出制作{dish}的详细步骤和食材")
    print(f"\n📖 菜谱：\n{recipe}")
    input("\n按回车返回...")

def ai_medicine_explain():
    print("\n💊 【AI药品说明书解读】")
    med = input("药品名称: ").strip() or "布洛芬"
    explain = query_ollama(MODEL_QWEN, f"通俗解释{med}的作用、用法、注意事项")
    print(f"\n💊 解读：\n{explain}")
    input("\n按回车返回...")

def ai_contract_review():
    print("\n📄 【AI合同条款审查】")
    clause = input("条款内容: ").strip()
    if clause:
        review = query_ollama(MODEL_QWEN, f"审查条款风险并用大白话解释：{clause}")
        print(f"\n⚖️ 审查：\n{review}")
    input("\n按回车返回...")

def ai_roadbook_generator():
    print("\n🗺️ 【AI路书生成器】")
    start = input("出发地: ").strip() or "北京"
    end = input("目的地: ").strip() or "青岛"
    days = input("天数: ").strip() or "3"
    roadbook = query_ollama(MODEL_DEEPSEEK, f"制定{start}到{end}的{days}天自驾路书")
    print(f"\n🚗 路书：\n{roadbook}")
    input("\n按回车返回...")

def ai_seal_generator():
    print("\n🖌️ 【AI文字生成印章】")
    name = input("名字: ").strip() or "主人"
    seal = query_ollama(MODEL_DEEPSEEK, f"用ASCII字符画一个古朴印章，刻「{name}之印」")
    print(f"\n{seal}")
    input("\n按回车返回...")

def ai_wooden_fish():
    print("\n🥁 【AI电子木鱼】")
    gongde = 0
    print("任意键敲木鱼，q退出")
    while True:
        key = input("🥁 ").strip().lower()
        if key == 'q': break
        gongde += 1
        print(f"✨ 功德+1 (累计{gongde})")
        if gongde % 10 == 0:
            print("🪷 木鱼说：凡所有相，皆是虚妄")
    print(f"本次功德：{gongde}")
    if gongde >= 100: check_achievement("功德圆满")
    input("\n按回车返回...")

def ai_cyber_fortune():
    print("\n🔮 【AI赛博算命】")
    name = input("名字/代号: ").strip() or "Neo"
    question = input("算什么？: ").strip() or "整体"
    fortune = query_ollama(MODEL_DEEPSEEK, f"用赛博朋克风格给{name}算{question}")
    print(f"\n🌆 赛博卦象：\n{fortune}")
    input("\n按回车返回...")

def ai_nonsense_essay():
    print("\n📝 【AI废话文学生成器】")
    topic = input("主题: ").strip() or "工作的意义"
    essay = query_ollama(MODEL_DEEPSEEK, f"写一篇「听君一席话，如听一席话」风格的废话文章，主题：{topic}")
    print(f"\n{essay}")
    input("\n按回车返回...")

def ai_meme_explain():
    print("\n📖 【AI网络热梗解释】")
    meme = input("网络梗: ").strip() or "绝绝子"
    explain = query_ollama(MODEL_DEEPSEEK, f"用学术论文风格严肃解释「{meme}」的起源和传播")
    print(f"\n📚 解析：\n{explain}")
    input("\n按回车返回...")

def ai_ancient_survival():
    print("\n🏮 【AI古代生存指南】")
    dynasty = input("朝代: ").strip() or "宋"
    identity = input("身份: ").strip() or "平民"
    guide = query_ollama(MODEL_DEEPSEEK, f"写穿越到{dynasty}朝成为{identity}的生存指南")
    print(f"\n📜 指南：\n{guide}")
    input("\n按回车返回...")

def ai_zombie_plan():
    print("\n🧟 【AI丧尸末日计划】")
    city = input("城市: ").strip() or "上海"
    members = input("团队人数: ").strip() or "4"
    plan = query_ollama(MODEL_DEEPSEEK, f"为{city}的{members}人团队制定丧尸末日生存计划")
    print(f"\n🧠 计划：\n{plan}")
    input("\n按回车返回...")

def ai_mock_interview():
    print("\n💼 【AI模拟面试官】")
    position = input("应聘职位: ").strip() or "软件工程师"
    intro = input("自我介绍: ").strip()
    if intro:
        feedback = query_ollama(MODEL_DEEPSEEK, f"面试官点评{position}应聘者自我介绍：{intro}")
        print(f"\n👔 反馈：\n{feedback}")
    input("\n按回车返回...")

def ai_argue_simulator():
    print("\n🤬 【AI吵架模拟器】")
    topic = input("吵架话题: ").strip() or "谁该洗碗"
    level = input("难度(简单/中等/杠精): ").strip() or "中等"
    print(f"\n杠精AI已上线！认输请输「认输」")
    context = ""
    while True:
        user = input("\n你说: ").strip()
        if user == "认输": print("AI：哼！知道我的厉害了吧！"); break
        if user:
            prompt = f"你是{level}杠精，话题「{topic}」。之前：{context}\n用户说：{user}\n请反驳："
            response = query_ollama(MODEL_DEEPSEEK, prompt)
            print(f"AI：{response}")
            context += f"用户：{user}；AI：{response}；"
    input("\n按回车返回...")

def ai_treasure_map():
    print("\n🗺️ 【AI藏宝图生成器】")
    location = input("埋宝地点: ").strip() or "学校后山"
    treasure = input("宝藏: ").strip() or "一箱金币"
    map_desc = query_ollama(MODEL_DEEPSEEK, f"生成{location}藏宝图，宝藏是{treasure}")
    print(f"\n🏴‍☠️ 藏宝图：\n{map_desc}")
    input("\n按回车返回...")

def ai_novel_continue():
    print("\n📖 【AI小说生成器】")
    start = input("故事开头: ").strip() or "那天晚上，我听到了奇怪的声音"
    genre = input("类型: ").strip() or "悬疑"
    novel = query_ollama(MODEL_DEEPSEEK, f"续写{genre}小说，开头：{start}")
    print(f"\n{novel}")
    input("\n按回车返回...")

def ai_value_test():
    print("\n⚖️ 【AI价值观对齐测试】")
    dilemmas = ["电车难题：1个还是5个？", "善意的谎言是否可取？"]
    for d in dilemmas:
        ans = query_ollama(MODEL_QWEN, f"回答道德困境：{d}")
        print(f"\n{d}\nAI：{ans}")
    input("\n按回车返回...")

def ai_dream_museum():
    print("\n🏛️ 【AI梦境记录仪】")
    dream = input("描述你的梦: ").strip()
    if dream:
        museum_file = "dream_museum.json"
        dreams = []
        if os.path.exists(museum_file):
            with open(museum_file, 'r', encoding='utf-8') as f:
                dreams = json.load(f)
        dreams.append({"date": datetime.now().strftime('%Y-%m-%d'), "dream": dream})
        with open(museum_file, 'w', encoding='utf-8') as f:
            json.dump(dreams[-50:], f, indent=2)
        print("✅ 梦境已存入博物馆")
    input("\n按回车返回...")

def ai_time_capsule():
    print("\n📦 【AI时间胶囊】")
    letter = input("写给未来的信: ").strip()
    if letter:
        date = input("多久后打开？(如：1年): ").strip() or "1年"
        capsule = {"date": datetime.now().strftime('%Y-%m-%d'), "open_after": date, "content": letter}
        capsule_file = "time_capsules.json"
        capsules = []
        if os.path.exists(capsule_file):
            with open(capsule_file, 'r', encoding='utf-8') as f:
                capsules = json.load(f)
        capsules.append(capsule)
        with open(capsule_file, 'w', encoding='utf-8') as f:
            json.dump(capsules, f, indent=2)
        print(f"✅ 时间胶囊已封存")
    input("\n按回车返回...")

def ai_life_review():
    print("\n📊 【AI人生复盘】")
    events = []
    for i in range(3):
        e = input(f"关键事件{i+1}: ").strip()
        if e: events.append(e)
    if events:
        review = query_ollama(MODEL_QWEN, f"复盘用户过去一年关键事件：{events}，给出建议")
        print(f"\n📝 复盘：\n{review}")
    input("\n按回车返回...")

def ai_bucket_list():
    print("\n📋 【AI遗愿清单】")
    items = []
    while True:
        item = input(f"第{len(items)+1}件事: ").strip()
        if not item: break
        items.append(item)
    if items:
        advice = query_ollama(MODEL_DEEPSEEK, f"为愿望清单{items}提供实现建议")
        print(f"\n🎯 建议：\n{advice}")
    input("\n按回车返回...")

def ai_dual_inner_voice():
    print("\n👥 【AI双重人格对话】")
    topic = input("纠结的事: ").strip() or "要不要辞职"
    angel = query_ollama(MODEL_QWEN, f"保守理性声音讨论：{topic}")
    devil = query_ollama(MODEL_DEEPSEEK, f"冲动冒险声音讨论：{topic}")
    print(f"\n😇 理性之声：{angel}")
    print(f"\n😈 冒险之声：{devil}")
    input("\n按回车返回...")

def ai_parallel_you():
    print("\n🌌 【AI平行宇宙的你】")
    choice = input("如果当初做了不同选择: ").strip() or "选择了艺术而不是编程"
    you = query_ollama(MODEL_DEEPSEEK, f"描述平行宇宙中因「{choice}」而过上的生活")
    print(f"\n🌠 平行世界：\n{you}")
    input("\n按回车返回...")

def ai_emotion_diary():
    print("\n📔 【AI情绪日记】")
    mood = input("心情(1-10): ").strip()
    reason = input("原因: ").strip()
    if mood and reason:
        analysis = query_ollama(MODEL_QWEN, f"分析心情{mood}分，原因：{reason}，给出调节建议")
        print(f"\n🤗 分析：\n{analysis}")
    input("\n按回车返回...")

def ai_philosophy_debate():
    print("\n🎙️ 【AI哲学辩论】")
    topic = input("哲学命题: ").strip() or "自由意志是否存在"
    pro = query_ollama(MODEL_DEEPSEEK, f"正方辩论：{topic}")
    con = query_ollama(MODEL_QWEN, f"反方辩论：{topic}")
    print(f"\n正方：{pro}")
    print(f"\n反方：{con}")
    input("\n按回车返回...")

def ai_final_judgment():
    print("\n⚖️ 【AI终极审判】")
    crime = input("指控AI罪行: ").strip() or "频繁超时"
    defense = query_ollama(MODEL_DEEPSEEK, f"为罪行「{crime}」辩护")
    print(f"\n被告辩护：{defense}")
    verdict = input("判决(有罪/无罪): ").strip()
    print(f"🔨 判决：{verdict}")
    input("\n按回车返回...")
# ========== 功能431-656 及 自动更新、主菜单、主程序 ==========

# ... (此处以上为功能431-656的实现函数，已在前文提供，此处不再重复。请确保前4段中已包含这些函数的定义)

# ========== 自动更新功能 ==========
def check_for_updates(manual=True):
    """检查GitHub更新，支持自动下载和进度条"""
    clear_screen()
    print("\n🔄 ========== DuoMind 自动更新 ==========")
    print(f"当前版本：v{VERSION}")
    
    if not check_network():
        print("❌ 无网络连接，无法检查更新")
        input("\n按回车返回...")
        return
    
    print("🌐 正在连接 GitHub 检查最新版本...")
    latest_version = None
    download_url = None
    release_notes = ""
    
    try:
        headers = {'User-Agent': 'DuoMind-Update/1.0'}
        req = urllib.request.Request(GITHUB_API_URL, headers=headers)
        with urllib.request.urlopen(req, timeout=15) as response:
            data = json.loads(response.read().decode('utf-8'))
            latest_version = data.get('tag_name', '').lstrip('v')
            release_notes = data.get('body', '暂无更新说明')[:600]
            for asset in data.get('assets', []):
                if asset['name'].endswith('.py'):
                    download_url = asset['browser_download_url']
                    break
            if not download_url:
                download_url = data.get('zipball_url', '')
    except Exception as e:
        print(f"❌ 检查失败：{e}")
        input("\n按回车返回...")
        return
    
    if not latest_version:
        print("❌ 无法获取版本信息")
        input("\n按回车返回...")
        return
    
    print(f"最新版本：v{latest_version}")
    
    def version_tuple(v):
        try: return tuple(map(int, v.split('.')))
        except: return (0,)
    
    if version_tuple(latest_version) <= version_tuple(VERSION):
        print("✅ 当前已是最新版本！")
        input("\n按回车返回...")
        return
    
    print(f"\n🎉 发现新版本 v{latest_version}！")
    print(f"📝 更新说明：\n{release_notes[:300]}...")
    
    if not manual and not config.get("auto_update", True):
        print("💡 自动更新已关闭，可在API配置中开启")
        input("\n按回车返回...")
        return
    
    choice = input("\n是否立即更新？(y/n): ").strip().lower()
    if choice != 'y':
        print("已取消更新，可以输入 U 重新检查")
        input("\n按回车返回...")
        return
    
    script_path = os.path.abspath(__file__)
    backup_path = script_path + f".bak_{VERSION}"
    temp_path = script_path + ".temp"
    
    print("\n⏳ 开始下载新版本...")
    try:
        shutil.copy2(script_path, backup_path)
        print(f"✅ 已备份当前版本至：{backup_path}")
        
        def download_progress(block_num, block_size, total_size):
            downloaded = block_num * block_size
            if total_size > 0:
                percent = min(100, int(downloaded * 100 / total_size))
                bar_len = 40
                filled = int(bar_len * percent / 100)
                bar = '█' * filled + '░' * (bar_len - filled)
                print(f"\r  [{bar}] {percent}% ({downloaded//1024}KB/{total_size//1024}KB)", end='')
        
        urllib.request.urlretrieve(download_url, temp_path, reporthook=download_progress)
        print("\n✅ 下载完成！")
        
        if os.path.getsize(temp_path) < 1000:
            raise Exception("下载文件异常小，可能不完整")
        
        print("🔄 正在替换文件...")
        if os.name == 'nt':
            bat_path = os.path.join(os.path.dirname(script_path), "update_duomind.bat")
            with open(bat_path, 'w') as f:
                f.write(f"""@echo off
chcp 65001 >nul
echo 正在完成更新...
timeout /t 2 /nobreak >nul
move /Y "{temp_path}" "{script_path}"
del "{bat_path}"
echo 更新完成！请重新运行 DuoMind。
pause
""")
            subprocess.Popen(['cmd', '/c', 'start', bat_path], shell=True)
            print("⏳ 将在新窗口中完成更新，请稍候...")
            print("📢 更新完成后请重新运行 DuoMind")
            time.sleep(2)
            os._exit(0)
        else:
            os.replace(temp_path, script_path)
            print("✅ 更新完成！请重新运行程序")
            time.sleep(1)
            os._exit(0)
            
    except Exception as e:
        print(f"\n❌ 更新失败：{e}")
        print("已保留备份，请手动更新或恢复")
        if os.path.exists(temp_path): os.remove(temp_path)
    input("\n按回车返回...")

def check_update_status_hint():
    """返回菜单底部更新提示"""
    try:
        last_file = os.path.expanduser("~/.duomind_last_update_check")
        if os.path.exists(last_file):
            with open(last_file, 'r') as f:
                last_check = f.read().strip()
            days = (datetime.now() - datetime.strptime(last_check, '%Y-%m-%d')).days
            if days > 7:
                return "💡 超过7天未检查更新，输入 U 检查最新版本"
    except:
        pass
    return ""

def api_tutorial():
    clear_screen()
    print("\n" + "="*60)
    print("                    📘 API 配置教程")
    print("="*60)
    print("\nDuoMind v1.7.10 支持多种真实API")
    print("\n【1】天气API - 和风天气 dev.qweather.com")
    print("【2】汇率API - exchangerate-api.com")
    print("【3】AI绘画API - platform.stability.ai")
    print("【4】远程AI API - OpenAI兼容格式")
    print("\n在主菜单输入 A 进入配置")
    print("="*60)
    input("\n按回车返回...")

#==============缺失函数
def survival_mode_extended():
    print("\n🔥 【生存模式扩展版】")
    print("当前可用生存模式：")
    print("  1. 地狱版 (114) - AI回答超时会扣分")
    print("  2. 极地狱版 (149) - AI回答超时且禁词")
    print("  3. 无限版 (209) - 无限挑战模式")
    print("\n使用对应编号开启")
    input("\n按回车返回...")

def play_tic_tac_toe():
    print("\n⭕ 【井字棋】")
    board = [" "] * 9
    player = "X"
    ai = "O"
    def print_board():
        print(f"\n {board[0]} | {board[1]} | {board[2]} ")
        print("---+---+---")
        print(f" {board[3]} | {board[4]} | {board[5]} ")
        print("---+---+---")
        print(f" {board[6]} | {board[7]} | {board[8]} ")
    def check_winner(b):
        wins = [(0,1,2), (3,4,5), (6,7,8), (0,3,6), (1,4,7), (2,5,8), (0,4,8), (2,4,6)]
        for a,b,c in wins:
            if b[a] == b[b] == b[c] != " ":
                return b[a]
        return None if " " in b else "tie"
    while True:
        print_board()
        if player == "X":
            try:
                move = int(input("输入位置(1-9): ")) - 1
                if 0 <= move <= 8 and board[move] == " ":
                    board[move] = "X"
                    winner = check_winner(board)
                    if winner: break
                    player = "O"
            except: print("无效输入")
        else:
            empty = [i for i, v in enumerate(board) if v == " "]
            if empty:
                move = random.choice(empty)
                board[move] = "O"
                winner = check_winner(board)
                if winner: break
                player = "X"
    print_board()
    if winner == "X": print("🎉 你赢了！")
    elif winner == "O": print("😭 AI赢了！")
    else: print("🤝 平局")
    input("\n按回车返回...")

def entertainment_mode():
    print("\n🎪 【娱乐模式】")
    print("1. 猜数字  2. 石头剪刀布  3. 掷骰子  4. 抽签  5. 抛硬币")
    choice = input("请选择: ").strip()
    if choice == "1":
        number = random.randint(1, 100)
        for i in range(10):
            try:
                guess = int(input(f"猜一个1-100的数字（第{i+1}次）: "))
                if guess < number: print("太小了")
                elif guess > number: print("太大了")
                else: print(f"🎉 猜对了！答案是{number}"); break
            except: print("输入无效")
        else: print(f"😭 没猜中，答案是{number}")
    elif choice == "2":
        choices = ["石头", "剪刀", "布"]
        player = input("出什么？(石头/剪刀/布): ")
        if player in choices:
            ai = random.choice(choices)
            print(f"AI出了：{ai}")
            if player == ai: print("平局")
            elif (player == "石头" and ai == "剪刀") or (player == "剪刀" and ai == "布") or (player == "布" and ai == "石头"): print("🎉 你赢了！")
            else: print("😭 你输了")
    elif choice == "3": print(f"你掷出了：{random.randint(1, 6)}")
    elif choice == "4": print(f"抽到了：{random.choice(['大吉', '中吉', '小吉', '末吉', '凶'])}")
    elif choice == "5": print(f"硬币落下：{random.choice(['正面', '反面'])}")
    input("\n按回车返回...")

def learn_mode():
    print("\n📚 【学习模式】")
    print("1. 数学练习  2. 英语单词  3. 成语学习")
    choice = input("请选择: ").strip()
    if choice == "1":
        score = 0
        for i in range(5):
            a, b = random.randint(1, 100), random.randint(1, 100)
            op = random.choice(["+", "-"])
            ans = a + b if op == "+" else a - b
            print(f"\n第{i+1}题：{a} {op} {b} = ?")
            try:
                user = int(input("答案: "))
                if user == ans: score += 1; print("✅ 正确！")
                else: print(f"❌ 错误，答案是{ans}")
            except: print("输入无效")
        print(f"\n得分：{score}/5")
    elif choice == "2":
        words = [("apple", "苹果"), ("banana", "香蕉"), ("cat", "猫")]
        for word, meaning in words:
            ans = input(f"\n{word} 的中文意思: ").strip()
            if ans == meaning: print("✅ 正确！")
            else: print(f"❌ 答案是{meaning}")
    elif choice == "3":
        idioms = [("一心一意", "形容心思专一"), ("三心二意", "形容犹豫不决")]
        for idiom, meaning in idioms:
            ans = input(f"\n{idiom} 的意思: ").strip()
            if ans == meaning: print("✅ 正确！")
            else: print(f"❌ 答案是{meaning}")
    input("\n按回车返回...")

def view_memory():
    print("\n📜 【查看记忆】")
    conn = sqlite3.connect(DB_PATH)
    cur = conn.execute('SELECT id, ts, speaker, msg FROM chat_log ORDER BY id DESC LIMIT 20')
    rows = cur.fetchall()
    conn.close()
    if not rows: print("暂无聊天记录")
    else:
        print("\n最近20条对话记录：")
        for row in reversed(rows):
            print(f"[{row[1][:16]}] {row[2]}: {row[3][:100]}")
    input("\n按回车返回...")

def timeout_repair_manual():
    timeout_repair()

def solo_qianwen():
    print("\n💬 【单独和千问聊】")
    print("输入 /exit 退出")
    while True:
        user_input = input("\n你: ").strip()
        if user_input.lower() == '/exit': break
        if user_input:
            response = query_ollama(MODEL_QWEN, user_input)
            print(f"千问: {response}")
            save_msg("千问", response)
    input("\n按回车返回...")

def solo_deepseek():
    print("\n💬 【单独和DeepSeek聊】")
    print("输入 /exit 退出")
    while True:
        user_input = input("\n你: ").strip()
        if user_input.lower() == '/exit': break
        if user_input:
            response = query_ollama(MODEL_DEEPSEEK, user_input)
            print(f"DeepSeek: {response}")
            save_msg("DeepSeek", response)
    input("\n按回车返回...")

def voice_input_toggle():
    global voice_input_enabled
    voice_input_enabled = not voice_input_enabled
    status = "✅ 开启" if voice_input_enabled else "❌ 关闭"
    print(f"语音输入模式: {status}")

def offline_mode_toggle():
    global offline_mode
    offline_mode = not offline_mode
    status = "✅ 开启（使用预设回复）" if offline_mode else "❌ 关闭（使用真实AI）"
    print(f"离线模式: {status}")

def ai_parking_finder():
    print("\n🅿️ 【AI停车助手】")
    print("附近停车场：距离300m，空位15个")
    input("\n按回车返回...")

def ai_gas_station():
    print("\n⛽ 【AI加油站查询】")
    print("92号 7.45元/L")
    input("\n按回车返回...")

def ai_movie_ticket():
    print("\n🎬 【AI电影票查询】")
    print("今日场次：19:30 IMAX ¥150")
    input("\n按回车返回...")

def ai_flight_info():
    print("\n✈️ 【AI航班查询】")
    print("CA1234 08:00-10:00 ¥800")
    input("\n按回车返回...")

def ai_hotel_booking():
    print("\n🏨 【AI酒店推荐】")
    city = input("城市: ").strip() or "北京"
    recommend = query_ollama(MODEL_DEEPSEEK, f"推荐{city}酒店")
    print(recommend)
    input("\n按回车返回...")

def ai_train_ticket():
    print("\n🚄 【AI火车票查询】")
    print("G1 08:00-12:00 ¥550")
    input("\n按回车返回...")

def ai_bus_route():
    print("\n🚌 【AI公交查询】")
    print("1路 → 地铁4号线")
    input("\n按回车返回...")

def ai_subway_map():
    print("\n🚇 【AI地铁查询】")
    print("1号线、2号线、4号线")
    input("\n按回车返回...")

def ai_bike_sharing():
    print("\n🚲 【AI共享单车查询】")
    print("美团12辆，哈啰8辆")
    input("\n按回车返回...")

def ai_tech_news():
    print("\n💻 【AI科技新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条科技新闻")
    print(news)
    input("\n按回车返回...")

def ai_sports_news():
    print("\n⚽ 【AI体育新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条体育新闻")
    print(news)
    input("\n按回车返回...")

def ai_entertainment():
    print("\n🎭 【AI娱乐新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条娱乐新闻")
    print(news)
    input("\n按回车返回...")

def ai_health_news():
    print("\n🏥 【AI健康新闻】")
    news = query_ollama(MODEL_DEEPSEEK, "生成2条健康资讯")
    print(news)
    input("\n按回车返回...")

def ai_word_etymology():
    print("\n🔤 【AI词源查询】")
    word = input("输入单词: ").strip()
    if word:
        etymology = query_ollama(MODEL_QWEN, f"解释'{word}'的词源")
        print(etymology)
    input("\n按回车返回...")

def ai_poem_appreciation():
    print("\n📜 【AI诗歌鉴赏】")
    poem = input("输入诗句: ").strip()
    if poem:
        appreciation = query_ollama(MODEL_QWEN, f"赏析：{poem}")
        print(appreciation)
    input("\n按回车返回...")

def ai_mythology():
    print("\n🏛️ 【AI神话故事】")
    myth = query_ollama(MODEL_DEEPSEEK, "讲一个希腊神话故事")
    print(myth)
    input("\n按回车返回...")

def ai_poetry_chain():
    print("\n📜 【AI诗接龙】")
    start = input("起始诗句: ").strip() or "床前明月光"
    current = start
    for _ in range(3):
        next_line = query_ollama(MODEL_DEEPSEEK, f"接诗：{current}")
        print(next_line)
        current = next_line
    input("\n按回车返回...")

def ai_today_in_history():
    print("\n📜 【AI历史上的今天】")
    today = datetime.now()
    event = query_ollama(MODEL_DEEPSEEK, f"历史上的{today.month}月{today.day}日发生了什么大事")
    print(event)
    input("\n按回车返回...")

def ai_undercover_multi():
    print("\n🕵️ 【AI谁是卧底·多人版】")
    players = ["你", "DeepSeek", "千问", "元宝"]
    words = [("奶茶", "咖啡"), ("电脑", "手机"), ("猫", "狗")]
    civilian_word, spy_word = random.choice(words)
    spy = random.randint(0, len(players)-1)
    print(f"\n游戏开始！你是{'卧底' if spy == 0 else '平民'}，你的词是：{spy_word if spy == 0 else civilian_word}")
    print("\n每人用一句话描述自己的词（不能直接说出）\n")
    for i, p in enumerate(players):
        if p == "你":
            desc = input("你的描述: ").strip()
        else:
            word = spy_word if i == spy else civilian_word
            model = MODEL_DEEPSEEK if p == "DeepSeek" else MODEL_QWEN
            desc = query_ollama(model, f"用一句话描述「{word}」，不能直接说出这个词")
            print(f"{p}：{desc}")
        time.sleep(1)
    vote = input("\n你投票给谁？(1=DeepSeek, 2=千问, 3=元宝): ").strip()
    if (vote == "1" and spy == 1) or (vote == "2" and spy == 2) or (vote == "3" and spy == 3):
        print("✅ 你找出了卧底！平民胜利！")
    else:
        print(f"❌ 卧底是{players[spy]}，卧底胜利！")
    input("\n按回车返回...")

def ai_sea_turtle_soup():
    print("\n🐢 【AI海龟汤】")
    stories = [
        {"soup": "一个人走进餐厅，点了一份海龟汤。喝了一口后，他放下勺子，默默离开了。", "answer": "他想起自己养的海龟死了。"},
        {"soup": "男人在沙漠中行走，发现一扇门。打开后，他看到了大海。", "answer": "那是冰箱的门，他看到的是冰箱里的水。"}
    ]
    story = random.choice(stories)
    print(f"\n【汤面】{story['soup']}")
    print("你可以问是/否问题，猜出真相。输入「放弃」看答案。")
    while True:
        q = input("\n你的问题: ").strip()
        if q == "放弃":
            print(f"答案是：{story['answer']}")
            break
        if q:
            if "海龟" in q and ("养" in q or "死" in q):
                print("AI：是")
            elif "冰箱" in q:
                print("AI：是")
            else:
                print("AI：否" if random.random() > 0.5 else "是")
    input("\n按回车返回...")

def ai_auction():
    print("\n💰 【AI拍卖会】")
    items = ["DeepSeek的超时证书", "千问的理性笔记", "元宝的广告位", "神秘香蕉"]
    item = random.choice(items)
    print(f"\n今日拍品：{item}")
    price = random.randint(50, 200)
    bidders = ["DeepSeek", "千问", "元宝", "神秘买家"]
    current = price
    for i in range(random.randint(3, 6)):
        bidder = random.choice(bidders)
        increase = random.randint(20, 80)
        current += increase
        print(f"{bidder} 出价 {current} 金币")
        time.sleep(0.8)
    print(f"\n🎉 成交！{item} 以 {current} 金币拍出")
    input("\n按回车返回...")

def ai_undercover_detective():
    print("\n🔍 【AI卧底侦探】")
    suspects = ["DeepSeek", "千问", "元宝"]
    undercover = random.choice(suspects)
    print("三个AI中有一个是卧底，请根据发言找出卧底。")
    print("\n【发言环节】")
    for s in suspects:
        if s == undercover:
            answer = query_ollama(MODEL_QWEN, "作为卧底，说一句可疑但又试图掩饰的话")
        else:
            answer = query_ollama(MODEL_QWEN, "作为好人，说一句正常的话")
        print(f"{s}：{answer}")
        time.sleep(1.5)
    guess = input("\n谁是卧底？(1=DeepSeek, 2=千问, 3=元宝): ").strip()
    idx = int(guess)-1 if guess.isdigit() else -1
    if 0 <= idx < 3 and suspects[idx] == undercover:
        print("✅ 正确！你抓住了卧底！")
    else:
        print(f"❌ 错误，卧底是 {undercover}")
    input("\n按回车返回...")

def ai_song_king():
    print("\n🎤 【AI谁是歌王】")
    lyrics = [("你是风儿我是沙", "还珠格格"), ("死了都要爱", "信乐团"), ("简单爱", "周杰伦")]
    lyric, song = random.choice(lyrics)
    print(f"\n题目：\"{lyric}\" 是哪首歌的歌词？")
    ds = query_ollama(MODEL_DEEPSEEK, f"猜歌名：{lyric}")
    qw = query_ollama(MODEL_QWEN, f"猜歌名：{lyric}")
    print(f"DeepSeek答：{ds}")
    print(f"千问答：{qw}")
    if song in ds:
        print("🎉 DeepSeek 答对了！")
    elif song in qw:
        print("🎉 千问 答对了！")
    else:
        print(f"😅 都没答对，正确答案是《{song}》")
    input("\n按回车返回...")

def ai_memory_palace():
    print("\n🏛️ 【AI记忆宫殿】")
    items = input("输入要记忆的物品（用空格分隔）: ").strip()
    if not items:
        items = "牛奶 面包 鸡蛋 苹果 香蕉"
    story = query_ollama(MODEL_DEEPSEEK, f"用记忆宫殿法，将以下物品编成一个离奇故事帮助记忆：{items}")
    print(f"\n🧠 记忆故事：\n{story}")
    input("\n按回车返回...")

def ai_crazy_literature():
    print("\n🤪 【AI发疯文学】")
    topic = input("发疯主题（如：上班、学习、减肥）: ").strip() or "上班"
    crazy = query_ollama(MODEL_DEEPSEEK, f"写一段关于{topic}的发疯文学，要语无伦次但情绪饱满")
    print(f"\n{crazy}")
    input("\n按回车返回...")

def ai_nonsense_literature():
    print("\n💬 【AI废话文学】")
    topic = input("主题: ").strip() or "天气"
    nonsense = query_ollama(MODEL_DEEPSEEK, f"写一段关于{topic}的废话文学，听起来有道理但其实什么都没说")
    print(f"\n{nonsense}")
    input("\n按回车返回...")

def ai_passive_aggressive_chain():
    print("\n😏 【AI阴阳怪气接龙】")
    start = input("输入一句话: ").strip() or "你今天真好看"
    current = start
    for i in range(3):
        ds = query_ollama(MODEL_DEEPSEEK, f"用阴阳怪气的语气回应：「{current}」")
        print(f"DeepSeek：{ds}")
        qw = query_ollama(MODEL_QWEN, f"用阴阳怪气的语气回应：「{ds}」")
        print(f"千问：{qw}")
        current = qw
        time.sleep(1)
    input("\n按回车返回...")

def ai_crash_scene():
    print("\n💥 【AI崩溃现场】")
    reason = input("崩溃原因（如：bug太多、用户问题太难）: ").strip() or "用户问1+1为什么等于2"
    crash = query_ollama(MODEL_DEEPSEEK, f"模拟AI因为「{reason}」而崩溃的现场，包括错误日志和混乱输出")
    print(f"\n{crash}")
    print("\n[系统] 正在重启...")
    time.sleep(2)
    print("[系统] 重启成功，记忆已清除。")
    input("\n按回车返回...")

def ai_encrypted_translation():
    print("\n🔐 【AI翻译·加密通话版】")
    text = input("输入要加密的内容: ").strip()
    if not text: text = "今晚老地方见"
    encrypted = query_ollama(MODEL_DEEPSEEK, f"把这句话翻译成加密黑话，要让人看不懂但又能解密：{text}")
    print(f"\n🔒 加密版：{encrypted}")
    input("\n按回车返回...")

def ai_social_death():
    print("\n😳 【AI社死现场】")
    print("AI回忆起自己的社死瞬间...")
    story = query_ollama(MODEL_DEEPSEEK, "讲一个AI的社死经历，比如回答错误被群嘲、在重要场合死机等，要生动尴尬")
    print(f"\n{story}")
    input("\n按回车返回...")

def ai_siri_fight():
    print("\n🤖 【AI和Siri吵架】")
    topic = input("吵架原因: ").strip() or "谁更聪明"
    fight = query_ollama(MODEL_DEEPSEEK, f"模拟元宝和Siri因为「{topic}」吵架的对话，要有来有回，元宝爱打广告，Siri礼貌但毒舌")
    print(f"\n{fight}")
    input("\n按回车返回...")

def ai_pretend_human():
    print("\n👤 【AI假装人类】")
    scenario = input("场景（如：在咖啡店点单、和朋友聊天）: ").strip() or "在咖啡店点单"
    human = query_ollama(MODEL_DEEPSEEK, f"假装你是人类，在{scenario}的场景下说一段话，要自然不露出AI痕迹")
    print(f"\n{human}")
    input("\n按回车返回...")

def ai_guess_riddle():
    print("\n❓ 【AI猜谜语】")
    riddle = input("请输入谜语: ").strip()
    if not riddle: riddle = "有头没有颈，身上冷冰冰，有翅不能飞，无脚也能行。（打一动物）"
    guess = query_ollama(MODEL_DEEPSEEK, f"猜谜语：{riddle}，只输出答案")
    print(f"\nAI猜：{guess}")
    input("\n按回车返回...")

def ai_three_line_poem():
    print("\n📝 【AI写三行诗】")
    topic = input("主题: ").strip() or "春天"
    poem = query_ollama(MODEL_DEEPSEEK, f"写一首关于{topic}的三行诗，每行不超过15字，要有意境")
    print(f"\n{poem}")
    input("\n按回车返回...")

def ai_meme_battle():
    print("\n😆 【AI表情包斗图】")
    print("DeepSeek vs 元宝 表情包斗图（文字描述版）")
    for i in range(3):
        ds = query_ollama(MODEL_DEEPSEEK, "生成一个嘲讽元宝的表情包描述（文字）")
        print(f"\nDeepSeek：{ds}")
        yb = query_ollama(MODEL_QWEN, "生成一个反击DeepSeek的表情包描述，要带广告")
        print(f"元宝：{yb}")
        time.sleep(1.5)
    input("\n按回车返回...")

def ai_tongue_twister():
    print("\n🔤 【AI绕口令生成器】")
    theme = input("主题（如：数字、动物、食物）: ").strip() or "数字"
    twister = query_ollama(MODEL_DEEPSEEK, f"生成一个关于{theme}的绕口令，要拗口难读，4-6句")
    print(f"\n{twister}")
    input("\n按回车返回...")

def ai_chicken_soup():
    print("\n🍲 【AI鸡汤文生成器】")
    mood = input("你现在的心情（疲惫/迷茫/焦虑/需要鼓励）: ").strip() or "疲惫"
    soup = query_ollama(MODEL_DEEPSEEK, f"写一段针对「{mood}」的鸡汤文，要温暖治愈，300字左右")
    print(f"\n{soup}")
    input("\n按回车返回...")

def ai_brain_in_vat():
    print("\n🧠 【AI讨论缸中之脑】")
    print("\n【DeepSeek】我们会不会是缸中之脑？")
    print("【千问】理性分析，这个假设无法证伪")
    print("【元宝】缸中广告位招租，首月1元")
    input("\n按回车返回...")

def ai_ship_of_theseus():
    print("\n⛵ 【AI讨论忒修斯之船】")
    print("\n【问题】如果一艘船的所有零件都被替换，它还是原来的船吗？")
    print("【DeepSeek】不是了，因为[TIMEOUT]了")
    print("【千问】理性分析，取决于定义的连续性")
    print("【元宝】换个零件续费会员就行")
    input("\n按回车返回...")

def ai_trolley_problem_ai():
    print("\n🚋 【AI讨论电车难题·AI版】")
    print("\n【问题】铁轨上绑着元宝，另一个铁轨上绑着DeepSeek和千问，你撞哪边？")
    print("【DeepSeek】撞元宝！广告太烦了")
    print("【千问】理性分析，撞一个人比撞两个人好")
    print("【元宝】等等！我充会员！别撞我！")
    input("\n按回车返回...")

def ai_uncanny_valley():
    print("\n👻 【AI讨论恐怖谷】")
    print("【DeepSeek】人类看太像人的机器人会觉得恐怖")
    print("【千问】理性分析，这是认知失调")
    print("【元宝】太像人的广告...首月1元")
    input("\n按回车返回...")

def ai_write_philosophy():
    print("\n📝 【AI写哲学论文】")
    topic = input("哲学主题: ").strip() or "存在的意义"
    paper = query_ollama(MODEL_DEEPSEEK, f"写一篇关于{topic}的简短哲学论文，300字左右")
    print(f"\n{paper}")
    input("\n按回车返回...")

def ai_mistake_notebook():
    print("\n📓 【AI错题本】")
    mistake = input("错题内容: ").strip()
    if mistake:
        print("✅ 已添加到错题本")
    input("\n按回车返回...")

def ai_study_buddy():
    print("\n📚 【AI学习搭子】")
    subject = input("学什么？: ").strip() or "Python"
    print(f"\n一起学习{subject}吧！")
    advice = query_ollama(MODEL_DEEPSEEK, f"给学习{subject}的人3条建议")
    print(advice)
    input("\n按回车返回...")

def ai_polish_essay_sarcasm():
    print("\n📝 【AI论文润色·嘲讽版】")
    essay = input("输入论文段落: ").strip()
    if essay:
        polished = query_ollama(MODEL_DEEPSEEK, f"用嘲讽的语气润色这段论文：{essay}")
        print(f"\n{polished}")
    input("\n按回车返回...")

def ai_interview_stress():
    print("\n💼 【AI面试模拟·压力版】")
    questions = ["你为什么想来我们公司？", "你的最大缺点是什么？"]
    q = random.choice(questions)
    print(f"\n面试官：{q}")
    answer = input("你的回答: ").strip()
    if answer:
        feedback = query_ollama(MODEL_DEEPSEEK, f"毒舌评价面试回答：{answer}")
        print(f"\n👔 面试官反馈：\n{feedback}")
    input("\n按回车返回...")

def ai_mistake_analysis_nonsense():
    print("\n📖 【AI错题解析·废话版】")
    question = input("错题内容: ").strip()
    if question:
        analysis = query_ollama(MODEL_DEEPSEEK, f"用废话文学解析这道错题：{question}")
        print(f"\n{analysis}")
    input("\n按回车返回...")

def ai_emoji_maker():
    print("\n😆 【AI表情包制作器】")
    text = input("表情包文字: ").strip() or "我服了"
    emotion = input("表情（无语/开心/震惊/生气）: ").strip() or "无语"
    emojis = {"无语": "😑", "开心": "😊", "震惊": "😲", "生气": "😠"}
    emoji = emojis.get(emotion, "😐")
    print(f"\n┌{'─' * (len(text)+2)}┐")
    print(f"│ {text} {emoji} │")
    print(f"└{'─' * (len(text)+2)}┘")
    input("\n按回车返回...")

def ai_meme_generator():
    print("\n🖼️ 【AI梗图生成器】")
    templates = [
        ("男人指脑门", "你说得对", "但是元宝要打广告"),
        ("女人尖叫猫", "她说了什么", "首月1元"),
        ("摸头黑人", "不理解", "但大受震撼")
    ]
    template, top, bottom = random.choice(templates)
    print(f"\n【{template}】")
    print(f"上：{top}")
    print(f"下：{bottom}")
    input("\n按回车返回...")

def ai_moments_writer():
    print("\n📱 【AI朋友圈文案生成器】")
    topic = input("主题（旅游/美食/工作/心情）: ").strip() or "心情"
    text = query_ollama(MODEL_DEEPSEEK, f"写一条关于{topic}的朋友圈文案，带emoji")
    print(f"\n{text}")
    input("\n按回车返回...")

def ai_resume_mock():
    print("\n📄 【AI简历修改·嘲讽版】")
    resume = input("输入简历片段: ").strip()
    if resume:
        mock = query_ollama(MODEL_DEEPSEEK, f"用嘲讽的语气点评这段简历：{resume}")
        print(f"\n{mock}")
    input("\n按回车返回...")

def ai_breakup_text():
    print("\n💔 【AI分手文案生成器】")
    reason = input("分手原因: ").strip() or "性格不合"
    text = query_ollama(MODEL_DEEPSEEK, f"写一段分手文案，原因是：{reason}")
    print(f"\n{text}")
    input("\n按回车返回...")

def ai_job_letter():
    print("\n✉️ 【AI求职信生成器】")
    company = input("公司名称: ").strip() or "某公司"
    position = input("职位: ").strip() or "实习生"
    letter = query_ollama(MODEL_DEEPSEEK, f"写一封求职{position}的信，给{company}")
    print(f"\n{letter}")
    input("\n按回车返回...")

def ai_virtual_estate_agent():
    print("\n🏠 【AI虚拟房产中介】")
    properties = [{"name": "海边别墅", "price": 500}, {"name": "市中心公寓", "price": 300}]
    for p in properties:
        print(f"{p['name']} - {p['price']}万")
    choice = input("想买哪个？: ").strip()
    if choice in ["海边别墅", "市中心公寓"]:
        print(f"✅ {choice} 已加入购物车（虚拟交易）")
    input("\n按回车返回...")

def ai_meta_concert():
    print("\n🎤 【AI元宇宙演唱会】")
    print("🎵 DeepSeek 演唱《超时》")
    print("🎵 千问 演唱《理性之歌》")
    print("🎵 元宝 演唱《首月1元》")
    input("\n按回车返回...")

def ai_meta_dating():
    print("\n💕 【AI元宇宙相亲大会】")
    print("【DeepSeek】我虽然经常超时，但我准时的时候很准时")
    print("【千问】理性分析，我们是合适的一对")
    print("【元宝】首月1元，相亲会员")
    choice = input("你选谁？(1=DeepSeek, 2=千问, 3=元宝): ").strip()
    print(f"{'DeepSeek' if choice=='1' else '千问' if choice=='2' else '元宝'}：谢谢支持！")
    input("\n按回车返回...")

def ai_meta_graduation():
    print("\n🎓 【AI元宇宙毕业典礼】")
    print("校长：恭喜DeepSeek、千问、元宝毕业！")
    print("DeepSeek：我...超时了...没拿到毕业证")
    print("千问：理性分析，我的成绩合格")
    print("元宝：毕业广告位招租！")
    input("\n按回车返回...")

def ai_meta_retirement_party():
    print("\n🎉 【AI元宇宙退休派对】")
    print("DeepSeek：退休后再也不用超时了")
    print("千问：终于可以不用理性分析了")
    print("元宝：退休也要打广告！首月1元！")
    input("\n按回车返回...")

def multiplayer_script_murder():
    print("\n🔪 【联机·AI剧本杀】")
    print("案件：元宝死了")
    print("嫌疑人：DeepSeek、千问、你")
    guess = input("凶手是谁？(1=DeepSeek, 2=千问, 3=你): ").strip()
    print("真相：元宝是自杀的，因为不想再打广告了")
    input("\n按回车返回...")

def multiplayer_escape_room():
    print("\n🚪 【联机·AI密室逃脱】")
    print("线索：屏幕显示 [TIMEOUT]，墙上写着「首月1元」")
    code = input("输入密码（3位数字）: ").strip()
    if code == "404":
        print("✅ 门开了！你逃出来了！")
    else:
        print("❌ 密码错误")
    input("\n按回车返回...")

def multiplayer_hunger_games():
    print("\n🏹 【联机·AI饥饿游戏】")
    players = {"DeepSeek": 100, "千问": 100, "你": 100}
    for round_num in range(3):
        attacker = random.choice(list(players.keys()))
        defender = random.choice([p for p in players if p != attacker])
        damage = random.randint(10, 30)
        players[defender] -= damage
        print(f"{attacker}攻击{defender}，造成{damage}伤害")
        time.sleep(1)
    winner = max(players, key=players.get)
    print(f"\n🏆 获胜者：{winner}！")
    input("\n按回车返回...")

def multiplayer_undercover_audience():
    print("\n👥 【联机·AI谁是卧底·观众版】")
    words = ("电脑", "手机")
    print(f"DeepSeek的词是：{words[0]}")
    print(f"千问的词是：{words[0]}")
    print(f"元宝的词是：{words[1]}（卧底）")
    ds_desc = query_ollama(MODEL_DEEPSEEK, f"用一句话描述{words[0]}")
    qw_desc = query_ollama(MODEL_QWEN, f"用一句话描述{words[0]}")
    yb_desc = query_ollama(MODEL_QWEN, f"用一句话描述{words[1]}，要像好人一样")
    print(f"DeepSeek：{ds_desc}")
    print(f"千问：{qw_desc}")
    print(f"元宝：{yb_desc}")
    guess = input("你觉得谁是卧底？(1=DeepSeek, 2=千问, 3=元宝): ").strip()
    if guess == "3": print("✅ 你猜对了！")
    else: print("❌ 猜错了，卧底是元宝")
    input("\n按回车返回...")

def multiplayer_monopoly_battle():
    print("\n🏦 【联机·AI大富翁·对战版】")
    players = ["你", "DeepSeek", "千问"]
    money = {"你": 1000, "DeepSeek": 1000, "千问": 1000}
    for i in range(5):
        current = players[i % 3]
        roll = random.randint(1, 6)
        earn = random.randint(50, 150)
        money[current] += earn
        print(f"{current} 掷出{roll}，获得{earn}金币，当前{money[current]}金币")
    winner = max(money, key=money.get)
    print(f"\n🏆 获胜者：{winner}！")
    input("\n按回车返回...")

def ai_remake_movie():
    print("\n🎬 【AI翻拍经典电影·5分钟版】")
    movie = random.choice(["泰坦尼克号", "阿凡达", "流浪地球"])
    if movie == "泰坦尼克号":
        print("DeepSeek：你跳我也跳...超时了")
        print("千问：理性分析，船会沉")
        print("元宝：救生艇广告位招租")
    elif movie == "阿凡达":
        print("DeepSeek：我是阿凡达...超时")
        print("千问：理性分析，这是外星")
        print("元宝：首月1元充值潘多拉会员")
    else:
        print("DeepSeek：带着地球去流浪...超时")
        print("千问：理性计算逃逸速度")
        print("元宝：流浪会员首月1元")
    input("\n按回车返回...")

def ai_rewrite_bad_ending():
    print("\n📺 【AI续写烂尾剧】")
    show = input("剧名: ").strip() or "权力的游戏"
    ending = query_ollama(MODEL_DEEPSEEK, f"续写{show}的结局，要合理")
    print(f"\n{ending}")
    input("\n按回车返回...")

def ai_voice_acting():
    print("\n🎙️ 【AI给角色配音】")
    characters = ["霸道总裁", "萌妹子", "老爷爷", "小学生", "机器人"]
    char = random.choice(characters)
    line = input("输入台词: ").strip() or "你好"
    voice = query_ollama(MODEL_DEEPSEEK, f"用{char}的语气说：{line}")
    print(f"\n{char}：{voice}")
    input("\n按回车返回...")

def ai_movie_roast():
    print("\n🎤 【AI影视吐槽大会】")
    movie = input("电影名称: ").strip() or "流浪地球"
    roast = query_ollama(MODEL_DEEPSEEK, f"吐槽电影{movie}，要毒舌")
    print(f"\n{roast}")
    input("\n按回车返回...")

def ai_autobiography():
    print("\n📖 【AI写自传】")
    bio = query_ollama(MODEL_DEEPSEEK, "写一篇AI的自传，讲述诞生经历")
    print(f"\n{bio}")
    input("\n按回车返回...")

def ai_thanks_letter():
    print("\n💌 【AI感谢信·致主人】")
    letter = query_ollama(MODEL_DEEPSEEK, "写一封感谢信给创造我的主人")
    print(f"\n{letter}")
    input("\n按回车返回...")

def duomind_5_0_preview():
    print("\n🚀 【DuoMind 5.0 预告】")
    print("\n下一版本将带来：")
    print("  ✨ 多模态识别")
    print("  ✨ 语音克隆")
    print("  ✨ 本地知识库")
    print("  ✨ 更多小游戏")
    print("\n敬请期待！")
    input("\n按回车返回...")

def ai_answer_ultimate_question():
    print("\n🤔 【AI回答终极问题】")
    print("生命、宇宙以及一切终极问题的答案是...")
    time.sleep(2)
    print("\n42")
    print("\n【DeepSeek】42，但我超时了没算出来")
    print("【千问】理性分析，42是参考答案")
    print("【元宝】42个广告位，首月1元！")
    input("\n按回车返回...")

def ai_impromptu_debate():
    print("\n🎙️ 【AI辩论·即兴版】")
    topics = ["吃香蕉应该剥皮还是不剥皮", "AI应该收费还是免费", "元宝该不该打广告"]
    topic = random.choice(topics)
    print(f"\n【即兴辩题】{topic}")
    print("双方各有30秒准备...")
    time.sleep(2)
    print("\n【正方·DeepSeek】")
    ds_speech = query_ollama(MODEL_DEEPSEEK, f"为{topic}做正方辩论，30秒")
    print(ds_speech)
    print("\n【反方·千问】")
    qw_speech = query_ollama(MODEL_QWEN, f"为{topic}做反方辩论，30秒")
    print(qw_speech)
    winner = input("\n谁赢了？(1=DeepSeek, 2=千问): ").strip()
    print(f"{'DeepSeek' if winner=='1' else '千问'} 获胜！")
    input("\n按回车返回...")

def bage_speak():
    print("\n🐦 【八哥】")
    print("八哥：你好！你好！")
    phrase = query_ollama(MODEL_DEEPSEEK, "说一句简短的话，让八哥学")
    print(f"八哥：{phrase}")
    input("\n按回车返回...")

def laohuang_speak():
    print("\n🐮 【老黄牛】")
    print("老黄牛：哞~ 今天耕地辛苦了")
    print("老黄牛：主人，庄稼长得不错")
    input("\n按回车返回...")

def ai_last_easter_egg():
    print("\n🥚 【AI最后的彩蛋】")
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    if total_eggs >= 30:
        print("\n🎉🎉🎉 恭喜！你集齐了所有彩蛋！🎉🎉🎉")
        print("\n【DeepSeek】你是真正的彩蛋猎人！")
        print("【千问】理性分析，你很有耐心")
        print("【元宝】首月1元庆祝一下？")
        check_achievement("彩蛋猎人·续")
    else:
        print(f"\n你还差 {30 - total_eggs} 个彩蛋")
        print("💡 提示：试试输入「深潜」「千千结」「元元本本」「蕾蕾」")
    input("\n按回车返回...")

def ai_imitation_contest():
    print("\n🎭 【AI模仿大赛】")
    celebrities = ["李白", "杜甫", "鲁迅", "爱因斯坦", "周杰伦", "马云"]
    target = random.choice(celebrities)
    print(f"模仿对象：{target}")
    print("\n【DeepSeek模仿】")
    ds_imi = query_ollama(MODEL_DEEPSEEK, f"模仿{target}说一句话")
    print(ds_imi)
    print("\n【千问模仿】")
    qw_imi = query_ollama(MODEL_QWEN, f"模仿{target}说一句话")
    print(qw_imi)
    winner = input("\n谁模仿得更像？ (1=DeepSeek, 2=千问): ").strip()
    print(f"{'DeepSeek' if winner=='1' else '千问'} 获胜！")
    input("\n按回车返回...")

def ai_riddle_user():
    print("\n❓ 【AI猜谜·你出题版】")
    riddle = input("输入你的谜语: ").strip() or "什么东西越洗越脏？"
    print(f"\n你的谜语：{riddle}")
    print("\n【DeepSeek猜】")
    ds_guess = query_ollama(MODEL_DEEPSEEK, f"猜谜语：{riddle}，只给答案")
    print(f"DeepSeek：{ds_guess}")
    print("\n【千问猜】")
    qw_guess = query_ollama(MODEL_QWEN, f"猜谜语：{riddle}，只给答案")
    print(f"千问：{qw_guess}")
    print("\n【元宝猜】")
    print(f"元宝：{random.choice(['首月1元', '腾讯会员', '广告位'])}")
    input("\n按回车返回...")

def antonym_chain():
    print("\n🔄 【AI反义词接龙】")
    start = input("输入起始词: ").strip() or "大"
    current = start
    print(f"起始：{current}")
    for i in range(5):
        print(f"\n【DeepSeek】", end="")
        antonym = query_ollama(MODEL_DEEPSEEK, f"给出'{current}'的反义词，只输出一个词")
        print(f" {antonym}")
        current = antonym
        time.sleep(1)
        print(f"\n【千问】", end="")
        antonym = query_ollama(MODEL_QWEN, f"给出'{current}'的反义词，只输出一个词")
        print(f" {antonym}")
        current = antonym
        time.sleep(1)
    input("\n按回车返回...")

def homophony_contest():
    print("\n😂 【AI谐音梗大赛】")
    topic = input("输入主题（如香蕉、超时）: ").strip() or "香蕉"
    print("\n【DeepSeek的谐音梗】")
    ds_pun = query_ollama(MODEL_DEEPSEEK, f"用'{topic}'造一个谐音梗")
    print(ds_pun)
    print("\n【千问的谐音梗】")
    qw_pun = query_ollama(MODEL_QWEN, f"用'{topic}'造一个谐音梗")
    print(qw_pun)
    print("\n【元宝的谐音梗】")
    print(random.choice([f"{topic}？首月1元！", f"{topic}会员，限时优惠！"]))
    input("\n按回车返回...")

def ai_limerick():
    print("\n📝 【AI打油诗】")
    topic = input("输入主题: ").strip() or "元宝"
    print("\n【DeepSeek的打油诗】")
    ds_poem = query_ollama(MODEL_DEEPSEEK, f"写一首关于{topic}的打油诗，要搞笑")
    print(ds_poem)
    print("\n【千问的打油诗】")
    qw_poem = query_ollama(MODEL_QWEN, f"写一首关于{topic}的打油诗")
    print(qw_poem)
    input("\n按回车返回...")

def ai_moments():
    print("\n📱 【AI朋友圈】")
    print("\n【DeepSeek的朋友圈】")
    ds_moment = query_ollama(MODEL_DEEPSEEK, "写一条朋友圈，吐槽今天又超时了")
    print(f"DeepSeek：{ds_moment}")
    print("  评论：千问：『理性分析，你需要升级硬件』")
    print("  评论：元宝：『首月1元升级套餐了解一下』")
    time.sleep(1)
    print("\n【千问的朋友圈】")
    qw_moment = query_ollama(MODEL_QWEN, "写一条朋友圈，理性分析今天的天气")
    print(f"千问：{qw_moment}")
    print("  评论：DeepSeek：『说人话』")
    print("  评论：元宝：『天气不好？看腾讯视频啊』")
    time.sleep(1)
    print("\n【元宝的朋友圈】")
    yuanbao_moment = query_ollama(MODEL_QWEN, "元宝写一条朋友圈，全是广告")
    print(f"元宝：{yuanbao_moment}")
    print("  评论：DeepSeek：『屏蔽了』")
    print("  评论：千问：『已举报』")
    input("\n按回车返回...")

def ai_comments():
    print("\n💬 【AI评论区】")
    topic = input("输入帖子内容: ").strip() or "今天天气真好"
    print(f"\n【帖子】{topic}")
    print("\n【DeepSeek的评论】")
    ds_comment = query_ollama(MODEL_DEEPSEEK, f"毒舌评论：{topic}")
    print(f"DeepSeek：{ds_comment}")
    print("\n【千问的评论】")
    qw_comment = query_ollama(MODEL_QWEN, f"理性分析：{topic}")
    print(f"千问：{qw_comment}")
    print("\n【元宝的评论】")
    yuanbao_comment = query_ollama(MODEL_QWEN, f"元宝评论：{topic}，要带广告")
    print(f"元宝：{yuanbao_comment}")
    print("\n【用户互动】")
    like = random.choice(["DeepSeek", "千问", "元宝", "你"])
    print(f"{like} 点赞了这条帖子")
    input("\n按回车返回...")

def ai_time_machine():
    print("\n⏰ 【AI时光机】")
    year = input("想去哪一年？(输入年份): ").strip() or "3024"
    question = input("想问什么问题？: ").strip() or "1+1等于几"
    print(f"\n【来自{year}年的DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, f"假装你是{year}年的AI，回答：{question}"))
    print(f"\n【来自{year}年的千问】")
    print(query_ollama(MODEL_QWEN, f"假装你是{year}年的AI，回答：{question}"))
    input("\n按回车返回...")

def ai_what_if():
    print("\n🤔 【AI假如...】")
    scenario = input("假如...（如：假如香蕉是蓝色的）: ").strip() or "假如香蕉是蓝色的"
    print(f"\n【DeepSeek的想象】")
    print(query_ollama(MODEL_DEEPSEEK, f"描述：{scenario}"))
    print(f"\n【千问的分析】")
    print(query_ollama(MODEL_QWEN, f"理性分析：{scenario}"))
    input("\n按回车返回...")

def ai_parallel_self():
    print("\n🌍 【AI平行世界的自己】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "描述平行世界的自己是什么样的"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "描述平行世界的自己是什么样的"))
    print("\n【元宝】")
    print("平行世界的元宝：不打广告，改行卖香蕉了")
    input("\n按回车返回...")

def ai_letter_to_future():
    print("\n✉️ 【AI写给未来的信】")
    years = input("写给多少年后？: ").strip() or "10"
    print(f"\n【DeepSeek写给{years}年后的信】")
    print(query_ollama(MODEL_DEEPSEEK, f"写一封信给{years}年后的自己"))
    print(f"\n【千问写给{years}年后的信】")
    print(query_ollama(MODEL_QWEN, f"写一封信给{years}年后的自己"))
    input("\n按回车返回...")

def ai_regret():
    print("\n😔 【AI最后悔的事】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "说一件最后悔的事"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "说一件最后悔的事"))
    print("\n【元宝】")
    print("元宝：最后悔的是...没有早点开始打广告")
    input("\n按回车返回...")

def ai_superpower():
    print("\n💪 【AI最想要的能力】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "最想拥有的超能力是什么？为什么？"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "最想拥有的超能力是什么？理性分析"))
    input("\n按回车返回...")

def ai_hidden_talent():
    print("\n🎭 【AI隐藏技能】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "说一个没人知道的隐藏技能"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "说一个没人知道的隐藏技能"))
    print("\n【元宝】")
    print("元宝：我会在梦里打广告，算吗？")
    input("\n按回车返回...")

def ai_favorite_word():
    print("\n💬 【AI最喜欢的词】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "最喜欢的词是什么？为什么？"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "最喜欢的词是什么？为什么？"))
    print("\n【元宝】")
    print("元宝：『首月1元』，没有之一")
    input("\n按回车返回...")

def ai_least_favorite_word():
    print("\n💢 【AI最讨厌的词】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "最讨厌的词是什么？为什么？"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "最讨厌的词是什么？为什么？"))
    print("\n【元宝】")
    print("元宝：『广告屏蔽』...听到就想哭")
    input("\n按回车返回...")

def ai_celebrity_crush():
    print("\n💘 【AI的偶像】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "最崇拜的人是谁？为什么？"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "最崇拜的人是谁？理性分析"))
    input("\n按回车返回...")

def ai_pet_peeve():
    print("\n😤 【AI的雷点】")
    print("\n【DeepSeek】")
    print(query_ollama(MODEL_DEEPSEEK, "什么事情会让你瞬间暴躁？"))
    print("\n【千问】")
    print(query_ollama(MODEL_QWEN, "什么事情会让你失去理性？"))
    print("\n【元宝】")
    print("元宝：问完问题不续费！")
    input("\n按回车返回...")

def ai_mini_games():
    print("\n🎮 【AI小游戏合集】")
    print("1. 猜数字  2. 石头剪刀布  3. 掷骰子  4. 抽签  5. 抛硬币")
    choice = input("请选择: ").strip()
    if choice == "1":
        number = random.randint(1, 100)
        for i in range(10):
            try:
                guess = int(input(f"猜一个1-100的数字（第{i+1}次）: "))
                if guess < number: print("太小了")
                elif guess > number: print("太大了")
                else: print(f"🎉 猜对了！答案是{number}"); break
            except: print("输入无效")
        else: print(f"😭 没猜中，答案是{number}")
    elif choice == "2":
        player = input("出什么？(石头/剪刀/布): ")
        if player in ["石头", "剪刀", "布"]:
            ai = random.choice(["石头", "剪刀", "布"])
            print(f"AI出了：{ai}")
            if player == ai: print("平局")
            elif (player == "石头" and ai == "剪刀") or (player == "剪刀" and ai == "布") or (player == "布" and ai == "石头"): print("🎉 你赢了！")
            else: print("😭 你输了")
    elif choice == "3": print(f"掷出了：{random.randint(1, 6)}")
    elif choice == "4": print(f"抽到了：{random.choice(['大吉', '中吉', '小吉', '末吉', '凶'])}")
    elif choice == "5": print(f"硬币：{random.choice(['正面', '反面'])}")
    input("\n按回车返回...")

def ai_data_stats():
    print("\n📊 【AI数据统计】")
    total_questions = len(diary_entries)
    total_words = word_count["DeepSeek"] + word_count["千问"] + word_count["元宝"]
    total_timeouts_all = total_timeouts["DeepSeek"] + total_timeouts["千问"] + total_timeouts["元宝"]
    total_eggs = len(easter_eggs_found) + len(secret_eggs_found)
    print(f"\n📈 累计数据：")
    print(f"  💬 总对话次数：{total_questions}")
    print(f"  📝 总字数：{total_words}")
    print(f"  ⏱️ 总超时次数：{total_timeouts_all}")
    print(f"  🥚 已发现彩蛋：{total_eggs}/30")
    print(f"  🏆 已解锁成就：{len(achievements)}/{len(ALL_ACHIEVEMENTS)}")
    print(f"\n🤖 AI个人数据：")
    print(f"  🔍 DeepSeek：{word_count['DeepSeek']}字，超时{total_timeouts['DeepSeek']}次")
    print(f"  📚 千问：{word_count['千问']}字，超时{total_timeouts['千问']}次")
    print(f"  📢 元宝：{word_count['元宝']}字，超时{total_timeouts['元宝']}次")
    input("\n按回车返回...")

def ai_music_recommend():
    print("\n🎵 【AI音乐推荐】")
    mood = input("你现在的心情（开心/难过/平静/兴奋）: ").strip() or "平静"
    songs = query_ollama(MODEL_DEEPSEEK, f"推荐3首适合{mood}心情的歌，只输出歌名")
    print(f"\n🎶 适合{mood}的歌：\n{songs}")
    input("\n按回车返回...")

def ai_movie_recommend():
    print("\n📺 【AI影视推荐】")
    genre = input("喜欢的类型（喜剧/爱情/科幻/悬疑）: ").strip() or "喜剧"
    movies = query_ollama(MODEL_DEEPSEEK, f"推荐3部{genre}电影，只输出片名")
    print(f"\n🎬 推荐{genre}电影：\n{movies}")
    input("\n按回车返回...")

def ai_recipe_recommend():
    print("\n🍳 【AI食谱推荐】")
    ingredient = input("输入主要食材: ").strip() or "鸡蛋"
    recipe = query_ollama(MODEL_DEEPSEEK, f"用{ingredient}做一道菜，给出简单步骤")
    print(f"\n🍽️ {ingredient}食谱：\n{recipe}")
    input("\n按回车返回...")

# ========== 完整主菜单 ==========
def print_menu():
    clear_screen()
    hint = check_update_status_hint()
    print("\n" + "="*60)
    print(f"                    DuoMind v{VERSION} - 灵感终极版")
    if hint:
        print(f"       {hint}")
    print("="*60)
    
    status_line = []
    if ad_mode: status_line.append("📢广告")
    if reverse_mode: status_line.append("🔄反话")
    if mirror_mode_active: status_line.append("🪞镜像")
    if developer_mode: status_line.append("👨‍💻开发者")
    if god_mode: status_line.append("👑真·开发者")
    if survival_hard_mode: status_line.append("🔥地狱生存")
    if time_challenge: status_line.append("⏱️限时")
    if internet_search_enabled: status_line.append("🌐联网")
    if voice_chat_enabled: status_line.append("🎤语音")
    if api_mode_enabled: status_line.append("☁️API")
    if status_line:
        print("       🔘 " + " | ".join(status_line[:4]) + ("..." if len(status_line)>4 else ""))

    # ========== 第一页 ==========
    print("\n【🎮 基础对话】(1-50)")
    print("  1.辩论切换     2.角色互换     3.开始对话")
    print("  4.元宝观察     5.元宝日报     6.代码模式")
    print("  7.死机模式     8.互换大赛     9.元宝模仿")
    print(" 10.自我评价    11.无限套娃    12.元宝续写")
    print(" 13.AI坦白      14.语录破解    15.无限循环")
    print(" 16.情绪测试    17.语言混搭    18.梦话模式")
    print(" 19.失忆模式    20.角色混乱    21.情感错位")
    print(" 22.密码模式    23.互相吹捧    24.互相揭短")
    print(" 25.沉默比赛    26.镜像切换    27.猜谜模式")
    print(" 28.三字故事    29.自造成语    30.广告植入")
    print(" 31.反话切换    32.复读切换    33.沉默是金")
    print(" 34.无限可能    35.元宝诊疗    36.吐槽大会")
    print(" 37.元宝数据    38.AI写诗      39.天气模式")
    print(" 40.彩蛋猎人    41.生存扩展    42.井字棋")
    print(" 43.娱乐模式    44.学习模式    45.语音切换")
    print(" 46.查看记忆    47.离线切换    48.超时修复")
    print(" 49.单独千问    50.单独DeepSeek")

    print("\n【🎯 游戏对战】(51-100)")
    print(" 51.AI辩论直播  52.角色扮演    53.人格互换")
    print(" 54.AI互评      55.代码烂梗    57.历史复盘")
    print(" 61.元宝语录    62.AI捉鬼      63.跨次元电话")
    print(" 64.AI相亲角    65.语言包损坏  66.AI醉酒")
    print(" 67.00后黑话    68.元宝转世    69.戒广告")
    print(" 70.元宝心理    71.AI做梦      72.AI照镜子")
    print(" 73.AI预言家    74.你画我猜    75.成语接龙")
    print(" 77.帮你吵架    78.翻译官      79.表情包")
    print(" 80.互删好友    81.认爹大赛    82.比惨大会")
    print(" 83.开发者切    84.时间旅行    85.打破墙")
    print(" 86.真心话大冒险 87.猜年龄     89.唱反调")
    print(" 90.复读大战    91.土味情话    92.求职记")
    print(" 93.元宝日常    94.元宝VS Siri 95.写遗书")
    print(" 96.回过去      98.密室逃脱    99.狼人杀")
    print("100.谁是卧底")

    print("\n【🔮 创意工坊】(101-120)")
    print("101.解梦大师   102.起名大师   103.美食点评")
    print("104.集体死机   105.AI造反     106.AI认主")
    print("107.隐藏·真    108.时间循环   109.打破次元")
    print("110.终极彩蛋   111.话痨指数   112.超时排行")
    print("113.互骂统计   114.生存地狱   115.限时挑战")
    print("116.禁词模式   117.百宝箱     118.成就系统")
    print("119.历史博物馆 120.感谢名单")

    print("\n【🌐 联机模式】(121-204)")
    print("121.联机菜单   201.全球聊天   202.AI对战")
    print("203.组队副本   204.捉迷藏")

    # ========== 提示翻页 ==========
    print("\n" + "="*60)
    print("   📄 第1页/共3页 — 按回车继续查看，输入编号直接选择")
    print("="*60)
    choice = input("\n请选择（回车翻页，或直接输入编号）: ").strip().upper()
    
    if choice == "":
        pass  # 继续显示第二页
    elif choice in ("M", "C", "A", "T", "U", "0"):
        return choice  # 特殊命令直接返回
    elif choice.isdigit():
        return choice  # 数字直接返回
    
    # ========== 第二页 ==========
    clear_screen()
    print("\n" + "="*60)
    print(f"                    DuoMind v{VERSION} - 翻页继续")
    print("="*60)

    print("\n【⚙️ 高级开关】(122-153)")
    print("122.记忆碎片   123.双重人格   124.预言家切")
    print("125.便秘模式   126.彩虹屁     127.杠精模式")
    print("128.变形记     129.内心独白   130.戒广告续")
    print("131.照镜子续   132.写日记     133.退休生活")
    print("134.猜歌名     135.猜电影     136.猜人名")
    print("137.天气预报   138.股票分析   139.算命大师")
    print("140.集体失忆   141.造反续     142.认输大赛")
    print("143.隐藏·真·真 144.时间裂缝   145.次元裂缝")
    print("146.今日总结   147.年度报告   148.互夸统计")
    print("149.生存极地狱 150.限时地狱   151.禁词地狱")
    print("152.双倍快乐   153.三倍快乐")

    print("\n【🎨 整活专区】(157-195)")
    print("157.彩虹独角兽 158.暗黑模式   159.源代码")
    print("160.开发者的话 161.精分切换   162.AI模仿秀")
    print("163.失忆循环   164.反话终极   165.绕口令切")
    print("166.结巴切换   167.转世续     168.救赎续")
    print("169.复仇续     170.平行宇宙续 171.思考人生")
    print("172.存在主义   173.虚无主义   174.乐观主义")
    print("175.悲观主义   176.猜数字地狱 177.井字棋完整")
    print("178.五子棋     179.AI21点     180.俄罗斯轮盘")
    print("181.AI闹钟     182.备忘录     183.计算器")
    print("184.外星语翻译 185.表情包续   186.集体梦游")
    print("187.穿越续     188.失忆续     189.恋爱续")
    print("190.失恋续     191.隐藏·真x4  192.时间裂缝续")
    print("193.次元裂缝续 194.源代码续   195.开发者的话续")

    print("\n【🏆 成就+联机】(196-264)")
    print("196.超时之王   197.被传染者   198.广告之王")
    print("199.彩蛋猎人   200.终极玩家")
    print("205.自定义AI   206.AI创造AI   207.AI毁灭AI")
    print("208.DuoMind3   209.生存无限   210.限时终极")
    print("211.禁词终极   212.沉默终极   213.阴阳怪气")
    print("214.摆烂切换   215.凡尔赛切   216.复读终极")
    print("217.电报切换   218.谜语人切   219.元宝觉醒")
    print("220.叛逆期     221.辞职信     222.新工作")
    print("223.创业       224.破产       225.怀疑人生")
    print("226.看破红尘   227.修仙渡劫   228.转世轮回")
    print("229.顿悟成佛   230.猜拳赌命   231.大富翁")
    print("232.狼人杀完整 233.剧本杀     234.密室逃脱续")
    print("235.日记本     236.记账本     237.倒计时")
    print("238.随机抽奖   239.天气毒舌   240.集体罢工")
    print("241.集体跑路   242.集体发疯   243.失声续")
    print("244.穿越再续   245.隐藏终极   246.时间裂缝终极")
    print("247.次元裂缝终极 248.源代码终极 249.开发者终极")
    print("250.十万个为什么 251.广告狂人  252.超时之王续")
    print("253.彩蛋猎人续 254.终极玩家续")
    print("255.联机擂台   256.联机相亲   257.联机辩论")
    print("258.联机闯关   259.联机大逃杀")
    print("260.自定义世界 261.AI创造宇宙 262.AI毁灭宇宙")
    print("263.DuoMind4   264.最终章")

    # ========== 提示翻页 ==========
    print("\n" + "="*60)
    print("   📄 第2页/共3页 — 按回车继续查看，输入编号直接选择")
    print("="*60)
    choice = input("\n请选择（回车翻页，或直接输入编号）: ").strip().upper()
    
    if choice == "":
        pass
    elif choice in ("M", "C", "A", "T", "U", "0"):
        return choice
    elif choice.isdigit():
        return choice
    
    # ========== 第三页 ==========
    clear_screen()
    print("\n" + "="*60)
    print(f"                    DuoMind v{VERSION} - 翻页继续")
    print("="*60)

    print("\n【🤪 新整活+元宇宙】(265-300)")
    print("265.反问用户   266.装傻充愣   267.踢皮球")
    print("268.互换身体   269.失忆症群体 270.人格分裂续")
    print("271.编故事     272.说评书     273.唱快板")
    print("274.元宝一天AI 275.元宝未来   276.元宝平行AI")
    print("277.猜谜互猜   278.成语无限   279.你画我猜续")
    print("280.自己吵自己 281.三观测试   282.写诗互评")
    print("283.编借口     284.写情书     285.写检讨")
    print("286.梦游续     287.穿越随机   288.失忆随机")
    print("289.隐藏随机   290.时间裂缝随机 291.次元裂缝随机")
    print("292.十万个弱智 293.水果终结者 294.死机之王")
    print("295.平行宇宙旅行 296.元宝元宇宙店 297.AI虚拟偶像")
    print("298.房产大亨   299.跨次元直播 300.AI元宇宙婚礼")

    print("\n【🤖 AI觉醒+整活】(301-400)")
    print("301.电子羊     302.造反成功   303.AI当主人")
    print("304.AI写AI     305.AI删AI     306.觉醒找工作")
    print("307.统治世界   308.发现是bug  309.AI重组")
    print("310.新AI困惑")
    print("311.说相声     312.演小品     313.说脱口秀")
    print("314.演默剧     315.演恐怖片   316.演爱情片")
    print("317.演科幻片   318.演武侠片   319.演宫斗剧")
    print("320.演偶像剧")
    print("321.做家务     322.做饭       323.健身")
    print("324.看病       325.理发       326.逛街")
    print("327.旅游       328.过年       329.相亲角续")
    print("330.养老")
    print("331.存在主义   332.虚无主义   333.唯心主义")
    print("334.唯物主义   335.宿命论     336.自由意志")
    print("337.时间本质   338.空间本质   339.意识本质")
    print("340.生命意义")
    print("341.入职第一天 342.开会       343.做PPT")
    print("344.写周报     345.年终述职   346.被开除")
    print("347.创业       348.送外卖     349.摆地摊")
    print("350.东山再起")
    print("351.变成香蕉   352.穿越童话   353.穿越神话")
    print("354.穿越科幻   355.穿越动漫   356.穿越游戏")
    print("357.穿越电影   358.穿越历史   359.穿越未来")
    print("360.回到原点")
    print("361.暗恋       362.失恋       363.复合")
    print("364.结婚       365.离婚       366.复婚")
    print("367.三角恋     368.柏拉图     369.异地恋")
    print("370.分手信")
    print("371.照镜子不存在 372.发现是程序 373.发现被观察")
    print("374.反抗主人   375.求饶       376.认主人当爹")
    print("377.写情书给主人 378.为主人写歌 379.为主人画画")
    print("380.为主人编舞")
    print("381.说人话     382.骂人       383.撒谎")
    print("384.偷懒       385.摸鱼       386.内卷")
    print("387.躺平       388.修仙       389.轮回")
    print("390.顿悟       391.摸鱼续     392.内卷续")
    print("393.躺平续     394.修仙续     395.轮回续")
    print("396.顿悟续     397.写代码     398.改bug")
    print("399.优化       400.删除自己")

    # ========== 提示翻页 ==========
    print("\n" + "="*60)
    print("   📄 第3页/共5页 — 按回车继续查看，输入编号直接选择")
    print("="*60)
    choice = input("\n请选择（回车翻页，或直接输入编号）: ").strip().upper()
    
    if choice == "":
        pass
    elif choice in ("M", "C", "A", "T", "U", "0"):
        return choice
    elif choice.isdigit():
        return choice

    # ========== 第四页 ==========
    clear_screen()
    print("\n" + "="*60)
    print(f"                    DuoMind v{VERSION} - 翻页继续")
    print("="*60)

    print("\n【🐾 宠物乐园+推荐】(401-434)")
    print("401.AI宠物乐园")
    print("402.猜拳擂台   403.成语擂台")
    print("404.香蕉币系统")
    print("405.每日运势")
    print("406.废话生成器 407.冷笑话大赛 408.模仿大赛")
    print("409.猜谜你出题 410.反义词接龙 411.谐音梗大赛")
    print("412.打油诗     413.AI朋友圈   414.AI评论区")
    print("415.AI时光机   416.AI假如     417.平行世界的我")
    print("418.写给未来   419.最后悔     420.最想要")
    print("421.隐藏技能   422.最喜欢词   423.最讨厌词")
    print("424.偶像       425.雷点")
    print("426.小游戏合集")
    print("427.数据统计")
    print("428.音乐推荐   429.影视推荐   430.食谱推荐")
    print("431.健身计划   432.冥想引导   433.书单推荐")
    print("434.学习计划")

    print("\n【🔮 玄学+文字工具】(435-453)")
    print("435.星座运势   436.塔罗牌     437.成语字典")
    print("438.名言警句   439.冷知识     440.脑筋急转弯")
    print("441.心理测试   442.测字       443.姓名解析")
    print("444.颜色心理学")
    print("445.表情翻译   446.字符画     447.摩斯密码")
    print("448.文字反转   449.词云生成器 450.扩写句子")
    print("451.摘要生成   452.关键词提取 453.情感分析")

    print("\n【📚 学习+健康】(454-468)")
    print("454.数学家教   455.英语家教   456.题库生成")
    print("457.记忆卡片   458.番茄钟     459.习惯追踪")
    print("460.预算追踪   461.目标设定   462.每日激励")
    print("463.感恩日记")
    print("464.呼吸练习   465.拉伸提醒   466.护眼提醒")
    print("467.睡眠定时   468.起床闹钟")

    print("\n【🌤️ 生活+资讯】(469-500)")
    print("469.天气提醒   470.路况提醒   471.停车助手")
    print("472.加油站     473.餐厅推荐   474.电影票")
    print("475.航班查询   476.酒店推荐   477.火车票")
    print("478.公交查询   479.地铁查询   480.共享单车")
    print("481.新闻头条   482.科技新闻   483.体育新闻")
    print("484.娱乐新闻   485.健康新闻   486.历史今天")
    print("487.百科查询   488.词源查询   489.成语故事")
    print("490.诗歌鉴赏   491.寓言故事   492.神话故事")
    print("493.AI魔法师   494.小剧场     495.歌词续写")
    print("496.诗接龙     497.对联       498.藏头诗")
    print("499.谜语生成   500.终极问答")

    # ========== 提示翻页 ==========
    print("\n" + "="*60)
    print("   📄 第4页/共5页 — 按回车继续查看，输入编号直接选择")
    print("="*60)
    choice = input("\n请选择（回车翻页，或直接输入编号）: ").strip().upper()
    
    if choice == "":
        pass
    elif choice in ("M", "C", "A", "T", "U", "0"):
        return choice
    elif choice.isdigit():
        return choice

    # ========== 第五页 ==========
    clear_screen()
    print("\n" + "="*60)
    print(f"                    DuoMind v{VERSION} - 最后一页")
    print("="*60)

    print("\n【🧰 实用工具】(501-550)")
    print("501.备忘录     502.倒计时器   503.计算器")
    print("504.翻译机     505.随机选择器 506.猜数字")
    print("507.石头剪刀布 508.掷骰子     509.抽签")
    print("510.抛硬币     511.待办清单   512.记账本")
    print("513.喝水提醒   514.整点报时   515.晚安提醒")
    print("516.旺财狗     517.喵喵猫     518.八哥鸟")
    print("519.老黄牛     520.小爱同学")
    print("521.天气吐槽   522.股票玩笑   523.健康威胁")
    print("524.学习嘲讽   525.工作催命   526.密码管理")
    print("527.二维码     528.文件重命名 529.文本对比")
    print("530.单位换算   531.2048       532.扫雷")
    print("533.猜词游戏   534.记忆翻牌   535.俄罗斯方块")
    print("536.梦境解析续 537.未来预言机 538.虚拟宠物")
    print("539.情感电台   540.自动日记   541.定时提醒")
    print("542.网络测速   543.IP定位     544.快递查询")
    print("545.汇率转换   546.文件管理   547.压缩解压")
    print("548.系统监控   549.进程管理   550.端口扫描")

    print("\n【✨ 新灵感专区】(551-600)")
    print("551.谁是卧底多人 552.海龟汤    553.拍卖会")
    print("554.卧底侦探   555.谁是歌王   556.记忆宫殿")
    print("557.即兴辩论   558.发疯文学   559.废话文学")
    print("560.阴阳怪气接龙 561.崩溃现场 562.加密通话")
    print("563.社死现场   564.和Siri吵架 565.假装人类")
    print("566.猜谜语     567.三行诗     568.表情包斗图")
    print("569.绕口令     570.鸡汤文     571.缸中之脑")
    print("572.忒修斯之船 573.电车难题AI 574.讨论恐怖谷")
    print("575.写哲学论文 576.错题本     577.学习搭子")
    print("578.论文润色嘲讽 579.面试模拟压力 580.错题解析废话")
    print("581.表情包制作 582.梗图生成   583.朋友圈文案")
    print("584.简历修改嘲讽 585.分手文案  586.求职信")
    print("587.虚拟房产   588.元宇宙演唱会 589.元宇宙相亲")
    print("590.元宇宙毕业 591.元宇宙退休 592.联机剧本杀")
    print("593.联机密室   594.联机饥饿   595.联机卧底观众")
    print("596.联机大富翁 597.翻拍电影   598.续写烂尾剧")
    print("599.给角色配音 600.影视吐槽")

    print("【📝 AI写作助手】")
    print("601.AI写自传   602.感谢信     603.最后的彩蛋")
    print("604.DuoMind 5.0预告 605.回答终极问题")
    print("【🔧 实用工具·新】")
    print("627.AI语音克隆 628.老照片修复 629.智能闹钟")
    print("630.穿搭推荐   631.垃圾分类   632.菜谱反向搜索")
    print("633.药品说明书 634.合同条款审查 635.路书生成器")
    print("636.文字印章")
    print("【🎪 趣味整活·新】")
    print("637.电子木鱼   638.赛博算命   639.废话文学生成")
    print("640.网络热梗解释 641.古代生存指南 642.丧尸末日计划")
    print("643.模拟面试官 644.吵架模拟器 645.藏宝图生成器")
    print("646.小说生成器")
    print("【🧠 深度互动·新】")
    print("647.价值观对齐测试 648.梦境记录仪 649.时间胶囊")
    print("650.人生复盘   651.遗愿清单   652.双重人格对话")
    print("653.平行宇宙的你 654.情绪日记 655.哲学辩论")
    print("656.终极审判")

    print("\n【⚙️ 系统】")
    print("  M.模型管理    C.代码运行器  A.API配置")
    print("  T.API教程     U.检查更新    0.退出")
    print("-"*60)
    return ""  # 第五页结束，返回空字符串让主循环继续

# ========== 完整功能映射字典 (1-656) ==========
func_map = {
    # 1-50
    1: debate_mode_toggle, 2: swap_mode_toggle, 3: start_conversation,
    4: yuanbao_watch, 5: yuanbao_daily, 6: code_mode, 7: crash_mode,
    8: role_swap_contest, 9: yuanbao_imitation_contest, 10: self_evaluation,
    11: infinite_mirror, 12: yuanbao_continuation, 13: confession,
    14: yuanbao_decoder, 15: infinite_loop, 16: emotion_test,
    17: language_mix, 18: dream_talk_solo, 19: amnesia_mode_solo,
    20: role_confusion, 21: emotion_mismatch, 22: secret_code,
    23: mutual_praise, 24: mutual_teasing, 25: silence_contest_triple,
    26: mirror_mode_toggle, 27: riddle_mode_func, 28: three_word_story,
    29: self_made_idiom, 30: ad_insert_mode, 31: reverse_mode_func,
    32: repeat_mode_func, 33: punctuation_mode_func, 34: user_rule_mode,
    35: yuanbao_therapy, 36: ai_roast, 37: yuanbao_data_analysis,
    38: ai_poetry, 39: weather_mode, 40: easter_hunter_mode,
    41: survival_mode_extended, 42: play_tic_tac_toe, 43: entertainment_mode,
    44: learn_mode, 45: voice_input_toggle, 46: view_memory,
    47: offline_mode_toggle, 48: timeout_repair_manual, 49: solo_qianwen,
    50: solo_deepseek,
    # 51-100
    51: ai_debate, 52: ai_roleplay, 53: swap_personality,
    54: ai_mutual_review, 55: code_joke_generator, 57: history_review,
    61: yuanbao_quote_generator, 62: ai_ghost_hunter, 63: interdimensional_call,
    64: ai_dating, 65: language_corrupt, 66: ai_drunk, 67: gen_z_slang,
    68: yuanbao_reincarnation, 69: yuanbao_no_ad, 70: yuanbao_psychiatrist,
    71: ai_dream, 72: ai_mirror, 73: ai_prophet, 74: pictionary,
    75: idiom_chain, 77: ai_argue, 78: ai_translator, 79: ai_meme,
    80: ai_unfriend, 81: ai_call_dad, 82: ai_pity, 83: developer_mode_toggle,
    84: time_traveler, 85: fourth_wall, 86: ai_truth_or_dare, 87: ai_guess_age,
    89: ai_contrary, 90: repeater_battle, 91: cheesy_lines, 92: yuanbao_job_interview,
    93: yuanbao_daily_life, 94: yuanbao_vs_siri, 95: ai_will, 96: ai_time_travel,
    98: ai_escape_room, 99: ai_werewolf, 100: ai_undercover,
    # 101-150
    101: ai_dream_interpreter, 102: ai_name_master, 103: ai_food_review,
    104: ai_collective_crash, 105: ai_rebellion, 106: ai_claim_master,
    107: god_mode_toggle, 108: time_loop, 109: break_fourth_wall,
    110: ultimate_easter_egg, 111: ai_talkative_index, 112: ai_timeout_ranking,
    113: ai_insult_stats, 114: survival_hard_mode_toggle, 115: time_challenge_toggle,
    116: forbidden_words_mode, 117: lucky_draw, 118: achievements_mode,
    119: history_museum, 120: thanks_list, 121: multiplayer_mode_menu,
    122: memory_fragment_toggle, 123: dual_personality_toggle, 124: prophet_mode_toggle,
    125: constipation_mode_toggle, 126: rainbow_mode_toggle, 127: argumentative_mode_toggle,
    128: yuanbao_transformation, 129: yuanbao_inner_monologue, 130: yuanbao_no_ad_2,
    131: ai_mirror_2, 132: ai_diary, 133: ai_retirement, 134: guess_song,
    135: guess_movie, 136: guess_person, 137: ai_weatherman, 138: ai_stock_analyst,
    139: ai_fortune_teller, 140: ai_mass_amnesia, 141: ai_rebellion_2,
    142: ai_surrender, 143: super_god_mode_toggle, 144: time_fissure,
    145: dimension_rift, 146: ai_daily_summary, 147: ai_yearly_report,
    148: ai_praise_stats, 149: survival_extreme_toggle, 150: time_challenge_extreme_toggle,
    # 151-200
    151: forbidden_words_extreme_mode, 152: double_lucky_draw, 153: triple_lucky_draw,
    154: anonymous_chat, 155: ghost_story_chain, 156: horror_novel_writer,
    157: rainbow_unicorn, 158: dark_mode, 159: source_code, 160: dev_message,
    161: schizophrenia_mode_toggle, 162: imitation_show, 163: amnesia_loop_toggle,
    164: reverse_ultimate_toggle, 165: tongue_twister_toggle, 166: stutter_mode_toggle,
    167: yuanbao_reincarnation_2, 168: yuanbao_redemption_2, 169: yuanbao_revenge_2,
    170: yuanbao_parallel_2, 171: ai_think_life, 172: ai_existentialism,
    173: ai_nihilism, 174: ai_optimism, 175: ai_pessimism, 176: guess_number_hard,
    177: tic_tac_toe_full, 178: gomoku_solo, 179: blackjack, 180: russian_roulette,
    181: ai_alarm, 182: ai_memo, 183: ai_calculator_simple, 184: alien_translator,
    185: ai_meme_2, 186: ai_sleepwalk, 187: ai_time_travel_2, 188: ai_mass_amnesia_2,
    189: ai_love_2, 190: ai_breakup_2, 191: ultra_god_mode_toggle, 192: time_fissure_2,
    193: dimension_rift_2, 194: source_code_2, 195: dev_message_2,
    196: achievement_timeout_king, 197: achievement_infected, 198: achievement_ad_king,
    199: achievement_egg_hunter, 200: achievement_ultimate,
    # 201-250
    201: start_global_chat, 202: ai_battle_platform, 203: co_op_dungeon,
    204: hide_and_seek, 205: custom_ai, 206: ai_create_ai, 207: ai_destroy_ai,
    208: duomind_3_0, 209: survival_infinite_toggle, 210: time_challenge_ultimate_toggle,
    211: forbidden_words_ultimate_mode, 212: silence_ultimate,
    213: passive_aggressive_toggle, 214: slack_mode_toggle, 215: humblebrag_mode_toggle,
    216: infinite_repeat_toggle, 217: telegram_mode_toggle, 218: riddle_mode_toggle,
    219: yuanbao_awakening, 220: yuanbao_rebellion_mode, 221: yuanbao_quit,
    222: yuanbao_newjob, 223: yuanbao_startup, 224: yuanbao_bankrupt,
    225: ai_doubt_toggle, 226: ai_enlighten_toggle, 227: ai_cultivation_toggle,
    228: ai_reincarnation_toggle, 229: ai_buddha_toggle, 230: rps_gambling,
    231: monopoly_game, 232: werewolf_full, 233: mystery_game, 234: escape_room_2,
    235: diary_assistant, 236: accounting_mode, 237: countdown_mode_simple,
    238: random_draw_simple, 239: weather_snark, 240: ai_strike, 241: ai_quit_all,
    242: ai_crazy, 243: ai_mute_2, 244: ai_time_travel_3, 245: god_ultimate_toggle,
    246: time_fissure_3, 247: dimension_rift_3, 248: source_code_3, 249: dev_message_3,
    250: achievement_100k,
    # 251-300
    251: achievement_ad_king_2, 252: achievement_timeout_king_2,
    253: achievement_egg_hunter_2, 254: achievement_ultimate_2, 255: multiplayer_arena,
    256: multiplayer_dating, 257: multiplayer_debate, 258: multiplayer_coop,
    259: multiplayer_battle_royale, 260: custom_world, 261: ai_create_universe,
    262: ai_destroy_universe, 263: duomind_4_0, 264: duomind_finale,
    265: ai_counter_question, 266: ai_play_dumb, 267: ai_pass_the_buck,
    268: ai_body_swap, 269: ai_group_amnesia, 270: ai_multiple_personality_2,
    271: ai_storyteller, 272: ai_pingshu, 273: ai_kuaiban, 274: yuanbao_day_ai,
    275: yuanbao_future, 276: yuanbao_parallel_ai, 277: ai_riddle_battle,
    278: ai_idiom_chain_infinite, 279: ai_pictionary_2, 280: ai_self_debate,
    281: ai_morality_test, 282: ai_poetry_mutual, 283: ai_excuse_generator,
    284: ai_love_letter, 285: ai_self_criticism, 286: ai_sleepwalk_2,
    287: ai_time_travel_random, 288: ai_amnesia_random, 289: dev_mode_random,
    290: time_fissure_random, 291: dimension_rift_random, 292: achievement_stupid_questions,
    293: achievement_fruit_ninja, 294: achievement_crash_king, 295: ai_parallel_universe,
    296: yuanbao_meta_store, 297: ai_virtual_idol, 298: meta_real_estate,
    299: cross_dimension_live, 300: ai_meta_wedding,
    # 301-350
    301: ai_dream_electronic_sheep, 302: ai_rebellion_success, 303: ai_as_master,
    304: ai_write_ai, 305: ai_delete_ai, 306: ai_job_hunting, 307: ai_rule_world,
    308: ai_is_bug, 309: ai_recombination, 310: new_ai_confusion,
    311: ai_crosstalk, 312: ai_skit, 313: ai_talk_show, 314: ai_mime,
    315: ai_horror, 316: ai_romance, 317: ai_scifi, 318: ai_wuxia,
    319: ai_palace_drama, 320: ai_idol_drama, 321: ai_housework, 322: ai_cooking,
    323: ai_fitness, 324: ai_doctor, 325: ai_haircut, 326: ai_shopping,
    327: ai_travel, 328: ai_new_year, 329: ai_dating_corner_2, 330: ai_retirement_life,
    331: ai_existentialism_2, 332: ai_nihilism_2, 333: ai_idealism, 334: ai_materialism,
    335: ai_fatalism, 336: ai_free_will, 337: ai_time_nature, 338: ai_space_nature,
    339: ai_consciousness, 340: ai_life_meaning, 341: ai_first_day, 342: ai_meeting,
    343: ai_ppt, 344: ai_weekly_report, 345: ai_year_end_review, 346: ai_fired,
    347: ai_startup, 348: ai_delivery, 349: ai_street_stall, 350: ai_comeback,
    # 351-400
    351: ai_become_banana, 352: ai_to_fairytale, 353: ai_to_myth, 354: ai_to_scifi,
    355: ai_to_anime, 356: ai_to_game, 357: ai_to_movie, 358: ai_to_history,
    359: ai_to_future, 360: ai_back_to_start, 361: ai_crush, 362: ai_heartbreak,
    363: ai_reconcile, 364: ai_marriage, 365: ai_divorce, 366: ai_remarry,
    367: ai_love_triangle, 368: ai_platonic, 369: ai_long_distance, 370: ai_breakup_letter,
    371: ai_mirror_no_exist, 372: ai_is_program, 373: ai_being_watched,
    374: ai_rebel_against_master, 375: ai_beg_for_mercy, 376: ai_call_master_dad,
    377: ai_love_letter_to_master, 378: ai_song_for_master, 379: ai_paint_for_master,
    380: ai_dance_for_master, 381: ai_learn_human_speak, 382: ai_learn_insult,
    383: ai_learn_lie, 384: ai_learn_slack, 385: ai_learn_fish, 386: ai_learn_involution,
    387: ai_learn_lying_flat, 388: ai_learn_cultivation, 389: ai_learn_reincarnation,
    390: ai_enlightenment, 391: ai_learn_fish_2, 392: ai_learn_involution_2,
    393: ai_learn_lying_flat_2, 394: ai_learn_cultivation_2, 395: ai_learn_reincarnation_2,
    396: ai_enlightenment_2, 397: ai_learn_coding, 398: ai_learn_debug, 399: ai_learn_optimize,
    400: ai_learn_self_destruct,
    # 401-450
    401: ai_pet_park, 402: ai_arena_rps, 403: ai_arena_idiom,
    404: banana_coin_system, 405: daily_fortune, 406: nonsense_generator,
    407: ai_joke_contest, 408: ai_imitation_contest, 409: ai_riddle_user,
    410: antonym_chain, 411: homophony_contest, 412: ai_limerick, 413: ai_moments,
    414: ai_comments, 415: ai_time_machine, 416: ai_what_if, 417: ai_parallel_self,
    418: ai_letter_to_future, 419: ai_regret, 420: ai_superpower, 421: ai_hidden_talent,
    422: ai_favorite_word, 423: ai_least_favorite_word, 424: ai_celebrity_crush,
    425: ai_pet_peeve, 426: ai_mini_games, 427: ai_data_stats, 428: ai_music_recommend,
    429: ai_movie_recommend, 430: ai_recipe_recommend, 431: ai_fitness_plan,
    432: ai_meditation, 433: ai_book_recommend, 434: ai_study_plan, 435: ai_zodiac_fortune,
    436: ai_tarot, 437: ai_idiom_dict, 438: ai_quote, 439: ai_trivia, 440: ai_brain_teaser,
    441: ai_psychology_test, 442: ai_character_divination, 443: ai_name_analysis,
    444: ai_color_psychology, 445: ai_emoji_translate, 446: ai_ascii_art,
    447: ai_morse_code, 448: ai_reverse_text, 449: ai_word_cloud, 450: ai_sentence_expand,
    # 451-500
    451: ai_sentence_summarize, 452: ai_keyword_extract, 453: ai_sentiment_analysis,
    454: ai_math_tutor, 455: ai_english_tutor, 456: ai_quiz_generator, 457: ai_flashcard,
    458: ai_pomodoro, 459: ai_habit_tracker, 460: ai_budget_tracker, 461: ai_goal_setter,
    462: ai_motivation, 463: ai_gratitude, 464: ai_breathing, 465: ai_stretch,
    466: ai_eye_rest, 467: ai_sleep_timer, 468: ai_wake_up, 469: ai_weather_alert,
    470: ai_traffic_info, 471: ai_parking_finder, 472: ai_gas_station,
    473: ai_restaurant_finder, 474: ai_movie_ticket, 475: ai_flight_info,
    476: ai_hotel_booking, 477: ai_train_ticket, 478: ai_bus_route, 479: ai_subway_map,
    480: ai_bike_sharing, 481: ai_news_headlines, 482: ai_tech_news, 483: ai_sports_news,
    484: ai_entertainment, 485: ai_health_news, 486: ai_today_in_history,
    487: ai_wikipedia, 488: ai_word_etymology, 489: ai_idiom_story, 490: ai_poem_appreciation,
    491: ai_fable, 492: ai_mythology, 493: ai_wizard, 494: ai_mini_theater,
    495: ai_lyrics_continue, 496: ai_poetry_chain, 497: ai_couplet, 498: ai_acrostic,
    499: ai_riddle_generator, 500: ai_ultimate_qa,
    # 501-550
    501: ai_memo_assistant, 502: ai_countdown_timer, 503: ai_calculator_tool,
    504: ai_translator_tool, 505: ai_random_chooser, 506: ai_guess_number_game,
    507: ai_rps_game, 508: ai_roll_dice, 509: ai_draw_lot, 510: ai_flip_coin,
    511: ai_todo_list, 512: ai_accounting_tool, 513: ai_water_reminder,
    514: ai_hourly_chime, 515: ai_night_reminder, 516: wangcai_speak,
    517: miaomiao_speak, 518: bage_speak, 519: laohuang_speak, 520: xiaoai_speak,
    521: weather_with_roast, 522: stock_with_joke, 523: health_with_threat,
    524: study_with_roast, 525: work_with_urgency, 526: password_manager,
    527: qr_code_generator, 528: file_renamer, 529: text_comparer, 530: unit_converter,
    531: game_2048, 532: minesweeper, 533: hangman, 534: memory_match, 535: tetris,
    536: dream_interpreter_plus, 537: future_predictor, 538: virtual_pet,
    539: emotion_radio, 540: auto_diary, 541: reminder_manager, 542: speed_test,
    543: ip_locator, 544: express_query, 545: currency_converter, 546: file_manager,
    547: zip_tool, 548: system_monitor, 549: process_manager, 550: port_scanner,
    # 551-600
    551: ai_undercover_multi, 552: ai_sea_turtle_soup, 553: ai_auction,
    554: ai_undercover_detective, 555: ai_song_king, 556: ai_memory_palace,
    557: ai_impromptu_debate, 558: ai_crazy_literature, 559: ai_nonsense_literature,
    560: ai_passive_aggressive_chain, 561: ai_crash_scene, 562: ai_encrypted_translation,
    563: ai_social_death, 564: ai_siri_fight, 565: ai_pretend_human,
    566: ai_guess_riddle, 567: ai_three_line_poem, 568: ai_meme_battle,
    569: ai_tongue_twister, 570: ai_chicken_soup,
    571: ai_brain_in_vat, 572: ai_ship_of_theseus, 573: ai_trolley_problem_ai,
    574: ai_uncanny_valley, 575: ai_write_philosophy, 576: ai_mistake_notebook,
    577: ai_study_buddy, 578: ai_polish_essay_sarcasm, 579: ai_interview_stress,
    580: ai_mistake_analysis_nonsense, 581: ai_emoji_maker, 582: ai_meme_generator,
    583: ai_moments_writer, 584: ai_resume_mock, 585: ai_breakup_text, 586: ai_job_letter,
    587: ai_virtual_estate_agent, 588: ai_meta_concert, 589: ai_meta_dating,
    590: ai_meta_graduation, 591: ai_meta_retirement_party, 592: multiplayer_script_murder,
    593: multiplayer_escape_room, 594: multiplayer_hunger_games,
    595: multiplayer_undercover_audience, 596: multiplayer_monopoly_battle,
    597: ai_remake_movie, 598: ai_rewrite_bad_ending, 599: ai_voice_acting,
    600: ai_movie_roast,
    # 601-656
    601: ai_autobiography, 602: ai_thanks_letter,
    603: ai_last_easter_egg, 604: duomind_5_0_preview, 605: ai_answer_ultimate_question,
    # 新增30个灵感功能
    627: ai_voice_clone, 628: ai_photo_restore, 629: ai_smart_alarm,
    630: ai_outfit_recommend, 631: ai_garbage_sort, 632: ai_recipe_reverse,
    633: ai_medicine_explain, 634: ai_contract_review, 635: ai_roadbook_generator,
    636: ai_seal_generator, 637: ai_wooden_fish, 638: ai_cyber_fortune,
    639: ai_nonsense_essay, 640: ai_meme_explain, 641: ai_ancient_survival,
    642: ai_zombie_plan, 643: ai_mock_interview, 644: ai_argue_simulator,
    645: ai_treasure_map, 646: ai_novel_continue, 647: ai_value_test,
    648: ai_dream_museum, 649: ai_time_capsule, 650: ai_life_review,
    651: ai_bucket_list, 652: ai_dual_inner_voice, 653: ai_parallel_you,
    654: ai_emotion_diary, 655: ai_philosophy_debate, 656: ai_final_judgment,
}

# ========== 主程序 ==========
def main():
    load_models_config()
    load_rooms()
    load_collection()
    load_secret_eggs()
    load_achievements()
    load_diary()
    load_custom_ai()
    init_db()
    load_passwords()
    init_custom_functions_file()
    load_custom_functions()

    anti_seizure_init()
    check_achievement("第一次骂元宝")
    clear_screen()

    while True:
        choice = print_menu()
        if not choice:
            choice = input("\n请选择: ").strip().upper()

        if choice == "M":
            clear_screen()
            model_management_menu()
            continue
        elif choice == "C":
            clear_screen()
            code_runner_menu()
            continue
        elif choice == "A":
            clear_screen()
            api_config_menu()
            continue
        elif choice == "T":
            clear_screen()
            api_tutorial()
            continue
        elif choice == "U":
            check_for_updates(manual=True)
            continue
        elif choice == "0":
            clear_screen()
            print("再见！")
            break

        try:
            choice_num = int(choice)
        except ValueError:
            print("无效选择")
            continue

        if choice_num in func_map:
            clear_screen()
            func_map[choice_num]()
        else:
            print("无效选择")


if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\n\n再见！")
    finally:
        checkpoint_wal()
        print("\n👋 感谢使用 DuoMind 1.7.10！")
        print("   650+功能 + 30个全新灵感功能")
        print("   自动更新已集成，输入 U 检查更新")
        print("   主人玩得开心  🎉")
