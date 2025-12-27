import tkinter as tk
import winsound
import threading
import sys
import os
import time

PASSWORD = "123"
MAX_ATTEMPTS = 3
TOTAL_TIME = 2 * 60 * 60
attempts = 0
locked = False

def resource_path(filename):
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")
    return os.path.join(base_path, filename)

ALARM_FILE = resource_path("alarm.wav")

def play_alarm():
    winsound.PlaySound(ALARM_FILE, winsound.SND_FILENAME | winsound.SND_ASYNC | winsound.SND_LOOP)

def stop_alarm():
    winsound.PlaySound(None, winsound.SND_PURGE)

root = tk.Tk()
root.overrideredirect(True)
root.attributes("-topmost", True)
root.geometry(f"{root.winfo_screenwidth()}x{root.winfo_screenheight()}")
root.configure(bg="red")

def fade_out(a=1.0):
    if a > 0:
        root.attributes("-alpha", a)
        root.after(40, fade_out, a - 0.03)
    else:
        stop_alarm()
        root.destroy()

def update_timer():
    global TOTAL_TIME
    if TOTAL_TIME <= 0:
        fade_out()
        return
    h = TOTAL_TIME // 3600
    m = (TOTAL_TIME % 3600) // 60
    s = TOTAL_TIME % 60
    timer_label.config(text=f"{h:02}:{m:02}:{s:02}")
    TOTAL_TIME -= 1
    root.after(1000, update_timer)

def glitch_bg():
    colors = ["black", "#001100", "#002200", "#00ff00"]
    root.configure(bg=colors[int(time.time()*20) % len(colors)])
    root.after(80, glitch_bg)

def hacker_screen():
    for w in root.winfo_children():
        w.destroy()
    root.configure(bg="black")
    glitch_bg()
    threading.Thread(target=play_alarm, daemon=True).start()
    rezzox = r"""
██████╗ ███████╗███████╗ ██████╗ ██╗  ██╗
██╔══██╗██╔════╝╚══███╔╝██╔═══██╗╚██╗██╔╝
██████╔╝█████╗    ███╔╝ ██║   ██║ ╚███╔╝
██╔══██╗██╔══╝   ███╔╝  ██║   ██║ ██╔██╗
██║  ██║███████╗███████╗╚██████╔╝██╔╝ ██╗
╚═╝  ╚═╝╚══════╝╚══════╝ ╚═════╝ ╚═╝  ╚═╝
"""
    msg = "ACCESS TERMINATED\n\nWE ARE UNIPAPEL\n\nVISUAL DEMO ONLY"
    tk.Label(root, text=rezzox, fg="#00ff00", bg="black", font=("Courier", 14)).pack(pady=20)
    tk.Label(root, text=msg, fg="#00ff00", bg="black", font=("Courier", 16)).pack()

def check_password():
    global attempts, locked
    if locked:
        return
    if entry.get() == PASSWORD:
        fade_out()
    else:
        attempts += 1
        status_label.config(text=f"ACCESS DENIED ({attempts}/{MAX_ATTEMPTS})")
        if attempts >= MAX_ATTEMPTS:
            locked = True
            entry.config(state="disabled")
            unlock_btn.config(state="disabled")
            hacker_screen()

tk.Label(root, text="WE ARE UNIPAPEL", fg="black", bg="red", font=("Courier", 10)).pack(pady=8)
center = tk.Frame(root, bg="red")
center.pack(expand=True)
tk.Label(center, text="REZOXX", fg="black", bg="red", font=("Courier", 48, "bold")).pack()
timer_label = tk.Label(center, text="02:00:00", fg="white", bg="black", font=("Courier", 24, "bold"))
timer_label.pack(pady=10)
status_label = tk.Label(center, text="ENTER SECURITY CODE", fg="white", bg="red", font=("Courier", 14))
status_label.pack()
entry = tk.Entry(center, font=("Courier", 18), justify="center")
entry.pack(pady=6)
entry.focus()
unlock_btn = tk.Button(center, text="UNLOCK", font=("Courier", 14), command=check_password)
unlock_btn.pack(pady=10)
tk.Label(root, text="WE ARE UNIPAPEL", fg="black", bg="red", font=("Courier", 10)).place(relx=0.99, rely=0.99, anchor="se")

update_timer()
root.mainloop()
