import tkinter as tk
from tkinter import ttk, messagebox
import json
import os
from datetime import datetime

data_file = "trainings.json"
trainings = []

if os.path.exists(data_file):
    try:
        with open(data_file, 'r', encoding='utf-8') as f:
            trainings = json.load(f)
    except:
        trainings = []
else:
    trainings = [
        {"date": "2024-01-15", "type": "Бег", "duration": 30},
        {"date": "2024-01-16", "type": "Плавание", "duration": 45},
        {"date": "2024-01-17", "type": "Силовая", "duration": 60},
        {"date": "2024-01-18", "type": "Йога", "duration": 40},
        {"date": "2024-01-19", "type": "Велосипед", "duration": 90}
    ]
    with open(data_file, 'w', encoding='utf-8') as f:
        json.dump(trainings, f, ensure_ascii=False, indent=2)

def validate_date(date_str):
    try:
        datetime.strptime(date_str, "%Y-%m-%d")
        return True
    except ValueError:
        return False

def validate_duration(duration_str):
    try:
        duration = float(duration_str)
        return duration > 0
    except ValueError:
        return False

def save_data():
    with open(data_file, 'w', encoding='utf-8') as f:
        json.dump(trainings, f, ensure_ascii=False, indent=2)

def refresh_table():
    for item in tree.get_children():
        tree.delete(item)
    
    filter_type_val = filter_type.get()
    filter_date_val = filter_date_entry.get().strip()
    
    filtered = trainings
    if filter_type_val != "Все":
        filtered = [t for t in filtered if t["type"] == filter_type_val]
    if filter_date_val:
        filtered = [t for t in filtered if filter_date_val in t["date"]]
    
    filtered.sort(key=lambda x: x["date"])
    
    for training in filtered:
        tree.insert("", "end", values=(training["date"], training["type"], training["duration"]))

def add_training():
    date_val = date_entry.get().strip()
    type_val = type_entry.get().strip()
    duration_val = duration_entry.get().strip()
    
    if not date_val or not type_val or not duration_val:
        messagebox.showerror("Ошибка", "Заполните все поля!")
        return
    
    if not validate_date(date_val):
        messagebox.showerror("Ошибка", "Неверный формат даты! Используйте ГГГГ-ММ-ДД")
        return
    
    if not validate_duration(duration_val):
        messagebox.showerror("Ошибка", "Длительность должна быть положительным числом!")
        return
    
    training = {
        "date": date_val,
        "type": type_val,
        "duration": float(duration_val)
    }
    trainings.append(training)
    save_data()
    refresh_table()
    duration_entry.delete(0, tk.END)
    messagebox.showinfo("Успех", "Тренировка добавлена!")

def delete_training():
    selected = tree.selection()
    if not selected:
        messagebox.showwarning("Предупреждение", "Выберите запись для удаления!")
        return
    
    item = selected[0]
    values = tree.item(item, "values")
    
    for i, training in enumerate(trainings):
        if (training["date"] == values[0] and 
            training["type"] == values[1] and 
            str(training["duration"]) == values[2]):
            del trainings[i]
            break
    
    save_data()
    refresh_table()
    messagebox.showinfo("Успех", "Запись удалена!")

def clear_filters():
    filter_type.set("Все")
    filter_date_entry.delete(0, tk.END)
    refresh_table()

root = tk.Tk()
root.title("Training Planner")
root.geometry("800x600")
root.resizable(True, True)

input_frame = ttk.LabelFrame(root, text="Добавить тренировку", padding=10)
input_frame.pack(fill="x", padx=10, pady=5)

ttk.Label(input_frame, text="Дата (ГГГГ-ММ-ДД):").grid(row=0, column=0, sticky="w", padx=5)
date_entry = ttk.Entry(input_frame, width=20)
date_entry.grid(row=0, column=1, padx=5, pady=5)
date_entry.insert(0, datetime.now().strftime("%Y-%m-%d"))

ttk.Label(input_frame, text="Тип тренировки:").grid(row=0, column=2, sticky="w", padx=5)
type_entry = ttk.Combobox(input_frame, values=["Бег", "Велосипед", "Плавание", "Силовая", "Йога"], width=15)
type_entry.grid(row=0, column=3, padx=5, pady=5)
type_entry.set("Бег")

ttk.Label(input_frame, text="Длительность (мин):").grid(row=0, column=4, sticky="w", padx=5)
duration_entry = ttk.Entry(input_frame, width=15)
duration_entry.grid(row=0, column=5, padx=5, pady=5)

add_btn = ttk.Button(input_frame, text="Добавить тренировку", command=add_training)
add_btn.grid(row=0, column=6, padx=10, pady=5)

filter_frame = ttk.LabelFrame(root, text="Фильтрация", padding=10)
filter_frame.pack(fill="x", padx=10, pady=5)

ttk.Label(filter_frame, text="Фильтр по типу:").grid(row=0, column=0, padx=5)
filter_type = ttk.Combobox(filter_frame, values=["Все", "Бег", "Велосипед", "Плавание", "Силовая", "Йога"], width=15)
filter_type.grid(row=0, column=1, padx=5)
filter_type.set("Все")

ttk.Label(filter_frame, text="Фильтр по дате:").grid(row=0, column=2, padx=5)
filter_date_entry = ttk.Entry(filter_frame, width=15)
filter_date_entry.grid(row=0, column=3, padx=5)

apply_filter_btn = ttk.Button(filter_frame, text="Применить фильтр", command=refresh_table)
apply_filter_btn.grid(row=0, column=4, padx=10)

clear_filter_btn = ttk.Button(filter_frame, text="Сбросить фильтр", command=clear_filters)
clear_filter_btn.grid(row=0, column=5, padx=5)

table_frame = ttk.Frame(root)
table_frame.pack(fill="both", expand=True, padx=10, pady=5)

scrollbar = ttk.Scrollbar(table_frame)
scrollbar.pack(side="right", fill="y")

tree = ttk.Treeview(table_frame, columns=("date", "type", "duration"), show="headings", yscrollcommand=scrollbar.set)
tree.heading("date", text="Дата")
tree.heading("type", text="Тип тренировки")
tree.heading("duration", text="Длительность (мин)")
tree.column("date", width=150)
tree.column("type", width=200)
tree.column("duration", width=150)

scrollbar.config(command=tree.yview)
tree.pack(fill="both", expand=True)

delete_btn = ttk.Button(root, text="Удалить выбранную запись", command=delete_training)
delete_btn.pack(pady=5)

refresh_table()

root.mainloop()
