import json
import time
import os
import sys
import random
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.firefox.options import Options
from selenium.webdriver.firefox.service import Service
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from datetime import datetime

class Colors:
    RESET = '\033[0m'
    BOLD = '\033[1m'
    RED = '\033[91m'
    GREEN = '\033[92m'
    YELLOW = '\033[93m'
    BLUE = '\033[94m'
    MAGENTA = '\033[95m'
    CYAN = '\033[96m'
    WHITE = '\033[97m'

def clear_screen():
    os.system('clear')

def print_logo():
    logo = f"""{Colors.MAGENTA}{Colors.BOLD}
╔═══════════════════════════════════════════════════════════╗
{Colors.CYAN}║                                                           ║
{Colors.RED}║   ███████╗██████╗ ███████╗███████╗                       ║
{Colors.RED}║   ██╔════╝╚════██╗██╔════╝██╔════╝                       ║
{Colors.YELLOW}║   █████╗  █████╔╝█████╗  █████╗                          ║
{Colors.YELLOW}║   ██╔══╝  ██╔═══╝ ██╔══╝  ██╔══╝                         ║
{Colors.GREEN}║   ███████╗███████╗███████╗███████╗                       ║
{Colors.GREEN}║   ╚══════╝╚══════╝╚══════╝╚══════╝                       ║
{Colors.CYAN}║                                                           ║
{Colors.BLUE}║    Hardened Facebook Messenger E2EE Tool                 ║
{Colors.CYAN}║                                                           ║
{Colors.MAGENTA}║              Created by: {Colors.YELLOW}SoLaR SquaD RuLeX {Colors.MAGENTA}              ║
{Colors.CYAN}║                                                           ║
{Colors.MAGENTA}╚═══════════════════════════════════════════════════════════╝
{Colors.RESET}"""
    print(logo)
    lines = logo.split('\n')
    for line in lines:
        print(line)
        time.sleep(0.05)
    print()

def print_separator(char='═', length=60, color=Colors.CYAN):
    print(f"{color}{char * length}{Colors.RESET}")

def print_success(message):
    print(f"{Colors.GREEN}{Colors.BOLD}✓ {message}{Colors.RESET}")

def print_error(message):
    print(f"{Colors.RED}{Colors.BOLD}✗ {message}{Colors.RESET}")

def print_info(message):
    print(f"{Colors.BLUE}{Colors.BOLD}ℹ {message}{Colors.RESET}")

def print_warning(message):
    print(f"{Colors.YELLOW}{Colors.BOLD}⚠ {message}{Colors.RESET}")

def animate_loading(text, duration=2):
    chars = ['⠋', '⠙', '⠹', '⠸', '⠼', '⠴', '⠦', '⠧', '⠇', '⠏']
    end_time = time.time() + duration
    i = 0
    while time.time() < end_time:
        sys.stdout.write(f'\r{Colors.CYAN}{chars[i % len(chars)]} {text}{Colors.RESET}')
        sys.stdout.flush()
        time.sleep(0.1)
        i += 1
    sys.stdout.write('\r' + ' ' * (len(text) + 5) + '\r')
    sys.stdout.flush()

class HardenedFacebookMessenger:
    def __init__(self):
        self.driver = None
        self.wait = None
        self.firefox_binary = None
        self.geckodriver_path = None
        self.haters_name = ""
        self.messages = []
        self.speed_seconds = 10
        self.target_uid = ""
        self.cookie_string = ""
        self.is_logged_in = False
        
    def find_firefox_binary(self):
        possible_paths = [
            '/data/data/com.termux/files/usr/bin/firefox',
            '/data/data/com.termux/files/usr/lib/firefox/firefox',
            os.path.expanduser('~/firefox/firefox'),
            'firefox'
        ]
        for path in possible_paths:
            if os.path.exists(path) and os.access(path, os.X_OK):
                return path
        return None
    
    def find_geckodriver(self):
        possible_paths = [
            '/data/data/com.termux/files/usr/bin/geckodriver',
            os.path.expanduser('~/geckodriver'),
            'geckodriver'
        ]
        for path in possible_paths:
            if os.path.exists(path) and os.access(path, os.X_OK):
                return path
        return None
    
    def load_messages_from_file(self, filepath):
        try:
            if not os.path.exists(filepath):
                print_error(f"File not found: {filepath}")
                return False
            with open(filepath, 'r', encoding='utf-8') as f:
                lines = f.readlines()
            self.messages = [line.strip() for line in lines if line.strip()]
            if not self.messages:
                print_error("No messages found in file")
                return False
            print_success(f"Loaded {len(self.messages)} messages from file")
            return True
        except Exception as e:
            print_error(f"Error reading file: {e}")
            return False
    
    def setup_driver(self):
        try:
            animate_loading("Setting up hardened browser", 2)
            self.firefox_binary = self.find_firefox_binary()
            self.geckodriver_path = self.find_geckodriver()
            if not self.firefox_binary:
                print_error("Firefox not found")
                print_info("Install with: pkg install firefox")
                return False
            if not self.geckodriver_path:
                print_error("Geckodriver not found")
                print_info("Install with: pkg install geckodriver")
                return False
            
            options = Options()
            options.add_argument('--headless')
            options.add_argument('--no-sandbox')
            options.add_argument('--disable-dev-shm-usage')
            options.add_argument('--disable-gpu')
            options.add_argument('--disable-extensions')
            options.add_argument('--disable-plugins')
            options.add_argument('--disable-images')
            options.add_argument('--disable-javascript')  # Wait, keep JS for Messenger
            options.add_argument('--memory-pressure-off')
            options.add_argument('--disable-background-timer-throttling')
            options.add_argument('--disable-renderer-backgrounding')
            options.add_argument('--disable-backgrounding-occluded-windows')
            options.add_argument('--user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36')
            options.add_argument('--disable-web-security')
            options.add_argument('--allow-running-insecure-content')
            
            # Anti-detection
            options.set_preference('dom.webdriver.enabled', False)
            options.set_preference('useAutomationExtension', False)
            
            # Network stability
            options.set_preference('network.http.pipelining', True)
            options.set_preference('network.http.proxy.pipelining', True)
            options.set_preference('network.http.pipelining.maxrequests', 8)
            options.set_preference('network.http.max-connections', 96)
            options.set_preference('network.http.max-connections-per-server', 32)
            options.set_preference('network.http.max-persistent-connections-per-server', 8)
            options.set_preference('network.http.connection-retry-timeout', 0)
            options.set_preference('network.http.connection-timeout', 120)
            options.set_preference('network.http.response.timeout', 300)
            options.set_preference('network.http.request.max-start-delay', 10)
            options.set_preference('network.http.keep-alive.timeout', 600)
            
            options.set_preference('browser.cache.disk.enable', False)
            options.set_preference('browser.cache.memory.enable', True)
            options.set_preference('browser.cache.memory.capacity', 65536)
            options.set_preference('dom.disable_beforeunload', True)
            options.set_preference('browser.tabs.remote.autostart', False)
            options.set_preference('dom.webnotifications.enabled', False)
            options.set_preference('media.volume_scale', '0.0')
            
            options.binary_location = self.firefox_binary
            service = Service(executable_path=self.geckodriver_path)
            self.driver = webdriver.Firefox(service=service, options=options)
            self.driver.set_page_load_timeout(180)
            self.driver.set_script_timeout(90)
            self.wait = WebDriverWait(self.driver, 45)
            print_success("Hardened setup completed")
            return True
        except Exception as e:
            print_error(f"Setup failed: {e}")
            return False

    def parse_cookies(self, cookie_string):
        cookies = []
        for pair in cookie_string.split(';'):
            pair = pair.strip()
            if '=' in pair:
                name, value = pair.split('=', 1)
                cookies.append({
                    'name': name.strip(),
                    'value': value.strip(),
                    'domain': '.facebook.com',
                    'path': '/',
                    'secure': True
                })
        return cookies

    def login_with_cookies(self):
        max_retries = 5
        for retry in range(max_retries):
            try:
                animate_loading(f"Authenticating (Attempt {retry + 1}/{max_retries})", 3)
                cookies = self.parse_cookies(self.cookie_string)
                self.driver.get("https://www.facebook.com")
                time.sleep(5)
                for cookie in cookies:
                    try:
                        self.driver.add_cookie(cookie)
                    except:
                        pass
                self.driver.refresh()
                time.sleep(6)
                indicators = [
                    "//a[@aria-label='Messenger']",
                    "//div[@role='navigation']",
                    "//div[@aria-label='Account']",
                    "//a[contains(@href, '/messages')]"
                ]
                for indicator in indicators:
                    try:
                        elements = self.driver.find_elements(By.XPATH, indicator)
                        if elements:
                            self.is_logged_in = True
                            return True
                    except:
                        continue
                print_warning("Login check failed, retrying...")
                time.sleep(10)
            except Exception as e:
                print_error(f"Login attempt failed: {e}")
                time.sleep(10)
        return False

    def check_login_status(self):
        try:
            indicators = [
                "//a[@aria-label='Messenger']",
                "//div[@role='navigation']",
                "//div[@aria-label='Account']"
            ]
            for indicator in indicators:
                elements = self.driver.find_elements(By.XPATH, indicator)
                if elements:
                    return True
            return False
        except:
            return False

    def re_login_if_needed(self):
        if not self.check_login_status():
            print_warning("Login expired, re-logging in...")
            self.is_logged_in = False
            if not self.login_with_cookies():
                print_error("Re-login failed")
                return False
            print_success("Re-logged in successfully")
        return True

    def open_conversation(self):
        max_retries = 5
        for attempt in range(max_retries):
            try:
                animate_loading(f"Opening E2EE conversation (Attempt {attempt + 1}/{max_retries})", 3)
                self.driver.get(f"https://www.facebook.com/messages/e2ee/t/{self.target_uid}")
                time.sleep(8)
                # Check if conversation loaded
                if self.driver.find_elements(By.XPATH, "//div[@aria-label='Message']"):
                    print_success("E2EE Conversation opened")
                    return True
                else:
                    print_warning("Conversation not loaded, retrying...")
                    time.sleep(10)
            except Exception as e:
                print_error(f"Error opening conversation: {e}")
                time.sleep(10)
        return False

    def send_single_message(self, message):
        max_attempts = 10
        for attempt in range(max_attempts):
            try:
                full_message = f"{self.haters_name} {message}" if self.haters_name else message
                selectors = [
                    "//div[@aria-label='Message'][@contenteditable='true']",
                    "//div[@role='textbox'][@contenteditable='true']",
                    "//div[@data-lexical-editor='true']",
                    "//p[@data-editor-content='true']",
                    "//div[contains(@class, 'notranslate')][@contenteditable='true']",
                ]
                message_box = None
                for selector in selectors:
                    elements = self.driver.find_elements(By.XPATH, selector)
                    if elements and elements[0].is_displayed():
                        message_box = elements[0]
                        break
                if not message_box:
                    js_script = """
                    var editables = document.querySelectorAll('[contenteditable="true"]');
                    for (var i = 0; i < editables.length; i++) {
                        var elem = editables[i];
                        if (elem.offsetParent !== null && 
                            (elem.getAttribute('role') === 'textbox' || 
                             elem.getAttribute('aria-label') === 'Message')) {
                            return elem;
                        }
                    }
                    return null;
                    """
                    message_box = self.driver.execute_script(js_script)
                if not message_box:
                    time.sleep(3)
                    continue
                self.driver.execute_script("arguments[0].scrollIntoView({behavior: 'instant', block: 'center'});", message_box)
                time.sleep(0.5)
                try:
                    message_box.click()
                except:
                    self.driver.execute_script("arguments[0].focus(); arguments[0].click();", message_box)
                time.sleep(0.5)
                self.driver.execute_script("""
                    var elem = arguments[0];
                    elem.focus();
                    elem.innerHTML = '';
                    elem.textContent = '';
                """, message_box)
                time.sleep(0.5)
                escaped_msg = (full_message.replace('\\', '\\\\').replace("'", "\\'").replace('"', '\\"').replace('\n', '\\n').replace('\r', '\\r').replace('\t', '\\t'))
                js_code = f"""
                var elem = arguments[0];
                var text = '{escaped_msg}';
                elem.textContent = text;
                elem.dispatchEvent(new Event('input', {{ bubbles: true, cancelable: true }}));
                elem.dispatchEvent(new Event('change', {{ bubbles: true, cancelable: true }}));
                elem.dispatchEvent(new InputEvent('input', {{ bubbles: true, cancelable: true, data: text }}));
                var range = document.createRange();
                var sel = window.getSelection();
                range.selectNodeContents(elem);
                range.collapse(false);
                sel.removeAllRanges();
                sel.addRange(range);
                return elem.textContent.length;
                """
                self.driver.execute_script(js_code, message_box)
                time.sleep(0.5)
                sent = False
                try:
                    actions = ActionChains(self.driver)
                    actions.send_keys(Keys.RETURN).perform()
                    sent = True
                except:
                    pass
                if not sent:
                    send_selectors = [
                        "//div[@aria-label='Send']",
                        "//div[@aria-label='Press enter to send']",
                        "//button[contains(@aria-label, 'Send')]",
                    ]
                    for selector in send_selectors:
                        try:
                            send_btn = self.wait.until(EC.element_to_be_clickable((By.XPATH, selector)))
                            send_btn.click()
                            sent = True
                            break
                        except:
                            continue
                if not sent:
                    js_send = """
                    var elem = arguments[0];
                    var enterEvent = new KeyboardEvent('keydown', {
                        key: 'Enter',
                        code: 'Enter',
                        keyCode: 13,
                        which: 13,
                        bubbles: true,
                        cancelable: true
                    });
                    elem.dispatchEvent(enterEvent);
                    """
                    self.driver.execute_script(js_send, message_box)
                    sent = True
                if sent:
                    return True
                time.sleep(3)
            except Exception as e:
                print_warning(f"Send attempt {attempt + 1} failed: {e}")
                time.sleep(5)
        return False

    def start_sending(self):
        message_index = 0
        message_count = 0
        consecutive_failures = 0
        max_consecutive_failures = 15
        clear_screen()
        print_logo()
        print_info("Hardened E2EE message sending started - Press Ctrl+C to stop")
        print_separator()
        print()
        while True:
            try:
                if not self.re_login_if_needed():
                    print_error("Cannot continue without login")
                    break
                if not self.open_conversation():
                    print_error("Cannot open conversation")
                    break
                message = self.messages[message_index]
                current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
                success = self.send_single_message(message)
                message_count += 1
                if success:
                    consecutive_failures = 0
                    print(f"{Colors.CYAN}{Colors.BOLD}┌─────────────────────────────────────────────────────────┐{Colors.RESET}")
                    print(f"{Colors.C
