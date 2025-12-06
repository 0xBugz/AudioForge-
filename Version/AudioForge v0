import os
import threading
from tkinter import (Tk, StringVar, filedialog, messagebox)
from tkinter import ttk
from pydub import AudioSegment
from PIL import Image, ImageTk 

# ----------------------------
# File and Folder Selection
# ----------------------------
def select_files():
    files = filedialog.askopenfilenames(
        title="Select Audio Files",
        filetypes=[("Audio Files", "*.mp3 *.wav *.flac *.ogg *.aac"), ("All Files", "*.*")]
    )
    if files:
        selected_files.set("\n".join(files))
        status_var.set(f"{len(files)} file(s) selected.")

def select_output_folder():
    folder = filedialog.askdirectory(title="Select Output Folder")
    if folder:
        output_folder.set(folder)
        status_var.set("Output folder selected.")

# ----------------------------
# Conversion Logic
# ----------------------------
def convert_thread():
    try:
        files = selected_files.get().split("\n")
        if not files or files == [""]:
            messagebox.showerror("Error", "Please select audio file(s).")
            return

        folder = output_folder.get()
        if not os.path.isdir(folder):
            messagebox.showerror("Error", "Please select a valid output folder.")
            return

        fmt = format_var.get().lower()

        progress["value"] = 0
        progress["maximum"] = len(files)
        status_var.set("Converting...")

        for i, file in enumerate(files):
            audio = AudioSegment.from_file(file)
            base_name = os.path.splitext(os.path.basename(file))[0]
            output_file = os.path.join(folder, f"{base_name}.{fmt}")

            # Avoid overwriting
            counter = 1
            while os.path.exists(output_file):
                output_file = os.path.join(folder, f"{base_name}({counter}).{fmt}")
                counter += 1

            audio.export(output_file, format=fmt)

            progress["value"] = i + 1
            status_var.set(f"Saved as: {os.path.basename(output_file)}")

        status_var.set("Conversion Completed")
        messagebox.showinfo("Success", f"{len(files)} file(s) converted successfully.")

    except Exception as e:
        status_var.set("Error occurred")
        messagebox.showerror("Conversion Error", str(e))
    finally:
        convert_btn.config(state="normal")

def convert_files():
    convert_btn.config(state="disabled")
    threading.Thread(target=convert_thread, daemon=True).start()

# ----------------------------
# GUI Setup
# ----------------------------
root = Tk()
root.title("AudioForge – forge your audio files into any format.")
root.geometry("700x650")
root.configure(bg="#1e1e1e")
root.resizable(False, False)

# ----------------------------
# Styling
# ----------------------------
style = ttk.Style()
style.theme_use("clam")

style.configure("TFrame", background="#2b2b2b")
style.configure("TLabel", background="#2b2b2b", foreground="white", font=("Segoe UI", 10))
style.configure("Header.TLabel", background="#1e1e1e", foreground="white",
                font=("Segoe UI", 22, "bold"))

style.configure("Accent.TButton", background="#3e8ef7", foreground="white",
                font=("Segoe UI", 11, "bold"), padding=6)
style.map("Accent.TButton", background=[("active", "#62a5ff")])

style.configure("Success.TButton", background="#28a745", foreground="white",
                font=("Segoe UI", 11, "bold"), padding=6)
style.map("Success.TButton", background=[("active", "#4bcc63")])

style.configure("TProgressbar", troughcolor="#3a3a3a",
                background="#4fd1c5", bordercolor="#3a3a3a")

style.configure("Status.TLabel", background="#111",
                foreground="white", padding=10, font=("Segoe UI", 10))

# ----------------------------
# Variables
# ----------------------------
selected_files = StringVar()
output_folder = StringVar()
format_var = StringVar(value="mp3")
status_var = StringVar(value="Ready")

# ----------------------------
# Logo Section
# ----------------------------
try:
    logo_image = Image.open("/home/cyber_dark/project/project/conveter/team_logo.jpeg")  # Replace with your actual logo filename
    logo_image = logo_image.resize((60, 60))  # Resize the logo
    logo_photo = ImageTk.PhotoImage(logo_image)
    ttk.Label(root, text="Powered by 0XBugz", 
          foreground="white", background="#1e1e1e",
          font=("Segoe UI",)).pack(pady=(10, 0))
    logo_label = ttk.Label(root, image=logo_photo, background="#1e1e1e")
    logo_label.image = logo_photo
    logo_label.pack(pady=(15, 5))
except Exception as e:
    print("Logo could not be loaded:", e)

# ----------------------------
# Header
# ----------------------------
ttk.Label(root, text="Audio Converter", style="Header.TLabel").pack(pady=10)

# ----------------------------
# File Selection Frame
# ----------------------------
file_frame = ttk.Frame(root, padding=15, style="TFrame")
file_frame.pack(padx=30, pady=10, fill="x")

ttk.Label(file_frame, text="Selected File(s):").pack(anchor="w")

ttk.Label(file_frame, textvariable=selected_files, foreground="#4aa3f0",
          wraplength=650, justify="left", background="#2b2b2b").pack(anchor="w", pady=3)

ttk.Button(file_frame, text="Browse Files", command=select_files,
           style="Accent.TButton", width=18).pack(pady=5)

# ----------------------------
# Output Folder Frame
# ----------------------------
folder_frame = ttk.Frame(root, padding=15, style="TFrame")
folder_frame.pack(padx=30, pady=10, fill="x")

ttk.Label(folder_frame, text="Output Folder:").pack(anchor="w")

ttk.Label(folder_frame, textvariable=output_folder, foreground="#4aa3f0",
          wraplength=650, background="#2b2b2b").pack(anchor="w", pady=3)

ttk.Button(folder_frame, text="Select Folder", command=select_output_folder,
           style="Accent.TButton", width=18).pack(pady=5)

# ----------------------------
# Format Selection
# ----------------------------
format_frame = ttk.Frame(root, style="TFrame")
format_frame.pack(pady=10, padx=30, fill="x")

ttk.Label(format_frame, text="Output Format: ").pack(side="left", padx=(0, 10))

format_combo = ttk.Combobox(format_frame, textvariable=format_var,
                            values=["mp3", "wav", "flac", "ogg", "aac"],
                            state="readonly", width=10)
format_combo.pack(side="left")

# ----------------------------
# Convert Button
# ----------------------------
convert_btn = ttk.Button(root, text="Convert Audio", command=convert_files,
                         style="Success.TButton", width=22)
convert_btn.pack(pady=20)

# ----------------------------
# Progress Bar
# ----------------------------
progress = ttk.Progressbar(root, length=500)
progress.pack(pady=10)

# ----------------------------
# Status Bar
# ----------------------------
status_label = ttk.Label(root, textvariable=status_var,
                         style="Status.TLabel", anchor="w")
status_label.pack(fill="x", side="bottom")

# ----------------------------
# Run App
# ----------------------------
root.mainloop()
