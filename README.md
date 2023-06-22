import tkinter as tk
import string
import random 
from tkinter import messagebox
import pyperclip
import json

Font_name="Courier"

# =============================================================================
                            # Password generator #
# =============================================================================
def gen_password():
    """
    info:randomly generates a password, 
    does not take any arguments.
    """
    
    global password_entry
    password_entry.delete(0, "end")

    letters_list = list(string.ascii_letters)
    numbers_list = list(string.digits)
    special_symbols = ["@","#","$","&"]
    charecter_lists = [letters_list, numbers_list, special_symbols]
    generated_password = []
    
    for i in range(random.randint(10, 15)):
        random_list = random.choice(charecter_lists)
        generated_password.append(random.choice(random_list))
    generated_password = "".join(generated_password)
    
    pyperclip.copy(generated_password)
    return password_entry.insert(0, generated_password)
# =============================================================================
                            # Adding Paswords into text file #
# =============================================================================

def add_info():
    """
    info:
        adds the entered info to a file already specified.
    """
    global website_entry
    global username_entry
    global password_entry
    
    website = website_entry.get()
    username = username_entry.get()
    password = password_entry.get()
    
    new_data = {
        website:{
            "username": username,
            "password": password,
            }
        }
    
    if website != "" and username != "" and password!="":
        
        message_box_input = messagebox.askokcancel(title="confirmation", message=f"""
This is the info you want to save:
username: {username}
password: {password}
click ok to save else cancel""")
        
        try:
            if message_box_input:
                    try: 
                        with open(file="passwords.json",mode="r") as passwordfile:
                            data = json.load(passwordfile)
                            data.update(new_data)
                    except:
                        with open(file="passwords.json",mode="w") as passwordfile:
                            json.dump(new_data,passwordfile,indent=4)
                    else:
                        with open(file="passwords.json",mode="w") as passwordfile:
                            json.dump(data,passwordfile,indent=4)
        except FileNotFoundError:
            print("worng filename or wrong mode used")
        finally:
                website_entry.delete(0,"end")
                username_entry.delete(0,"end")
                password_entry.delete(0,"end")
    else:
        messagebox.showerror(title="error message",message="an empty entry box found please check it.")
# =============================================================================
                            # Search Function #
# =============================================================================                    
def search_password():
    """
    info: retreves password from the file and puts it in the password entry.
    returns None
    """
    global website_entry
    global username_entry
    global password_entry
    
    website = website_entry.get()
    
    if website != "":
        username_entry.delete(0,"end")
        password_entry.delete(0,"end")
        
        with open(file="passwords.json",mode="r") as passwordfile:
            data = json.load(passwordfile)
            
        try:
            retreved_username = data[website]["username"]
            retreved_password = data[website]["password"]
        except KeyError:
            messagebox.showerror(title="errie message", message=f"{website} is not in the file.")
        else:
            username_entry.insert(0, retreved_username)
            password_entry.insert(0, retreved_password)
            pyperclip.copy(retreved_password)
    else:
        messagebox.showerror(title="error message",message="can not search for password as website entry is empty.")

# =============================================================================
                            # Window #
# =============================================================================

my_window = tk.Tk()
my_window.title("password manager")
my_window.minsize(500, 400)
my_window.configure(padx=100,pady=100,bg="black")
# =============================================================================
                            # Texts #
# =============================================================================
# website info
website_label = tk.Label(text="website :")
website_label.configure(font=("courier",20,"bold"),bg="black",fg="pink")
website_label.grid(column = 0, row = 0)

#email info
username_label = tk.Label(text="username:")
username_label.configure(font=("courier",20,"bold"),bg="black",fg="pink")
username_label.grid(column = 0, row = 1)

#password info
password_label = tk.Label(text="password:")
password_label.configure(font=("courier",20,"bold"),bg="black",fg="pink")
password_label.grid(column = 0, row = 2)

# =============================================================================
                            # Entries #
# =============================================================================
website_entry = tk.Entry(width= 40)
website_entry.grid(column = 1, row = 0,sticky="w")


username_entry = tk.Entry(width=85)
username_entry.grid(column = 1, row = 1, columnspan = 2,sticky="w")

password_entry = tk.Entry(width=40)
password_entry.grid(column = 1, row = 2,sticky="w")

# =============================================================================
                            # Buttons #
# =============================================================================
#genereate password button
generate_password = tk.Button(text="genreate button", width=25, command=gen_password)
generate_password.configure(bg="skyblue",fg="black",font=("courier",10,"bold"))
generate_password.grid(column = 2, row = 2, sticky="e") 

#add password button
add_password = tk.Button(text="ADD", width=63, command=add_info)
add_password.configure(bg="skyblue",fg="black",font=("courier",10,"bold"))
add_password.grid(column= 1, row=3, columnspan= 2,sticky="w")

#search button
search_button = tk.Button(text="search", width=30,command=search_password)
search_button.configure(bg="skyblue",fg="black",font=("courier",10,"bold"))
search_button.grid(column= 2,row=0,sticky="e")
# =============================================================================
                            # End #
# =============================================================================

my_window.mainloop()
