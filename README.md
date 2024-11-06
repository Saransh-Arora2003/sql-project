import tkinter as tk
from tkinter import messagebox
import mysql.connector

def connect_db():
    return mysql.connector.connect(
        host="localhost",
        user="root", 
        password="ansh@1234",
        database="user_management"
    )

def register_user():
    username = entry_username.get()
    password = entry_password.get()
    
    if username and password:
        try:
            conn = connect_db()
            cursor = conn.cursor()
            cursor.execute("INSERT INTO users (username, password) VALUES (%s, %s)", (username, password))
            conn.commit()
            messagebox.showinfo("Registration", "Registration successful!")
            entry_username.delete(0, tk.END)
            entry_password.delete(0, tk.END)
        except mysql.connector.Error as err:
            messagebox.showerror("Error", f"Error: {err}")
        finally:
            cursor.close()
            conn.close()
    else:
        messagebox.showwarning("Warning", "Please fill in both fields.")

def login_user():
    username = entry_username.get()
    password = entry_password.get()
    
    if username and password:
        try:
            conn = connect_db()
            cursor = conn.cursor()
            cursor.execute("SELECT * FROM users WHERE username=%s AND password=%s", (username, password))
            user = cursor.fetchone()
            if user:
                messagebox.showinfo("Login", "Login successful!")
            else:
                messagebox.showwarning("Login", "Invalid username or password.")
        except mysql.connector.Error as err:
            messagebox.showerror("Error", f"Error: {err}")
        finally:
            cursor.close()
            conn.close()
    else:
        messagebox.showwarning("Warning", "Please fill in both fields.")

root = tk.Tk()
root.title("Login and Registration Form")

tk.Label(root, text="Username").grid(row=0, column=0, padx=10, pady=10)
entry_username = tk.Entry(root)
entry_username.grid(row=0, column=1, padx=10, pady=10)

tk.Label(root, text="Password").grid(row=1, column=0, padx=10, pady=10)
entry_password = tk.Entry(root, show="*")
entry_password.grid(row=1, column=1, padx=10, pady=10)

button_register = tk.Button(root, text="Register", command=register_user)
button_register.grid(row=2, column=0, padx=10, pady=10)

button_login = tk.Button(root, text="Login", command=login_user)
button_login.grid(row=2, column=1, padx=10, pady=10)

root.mainloop()
