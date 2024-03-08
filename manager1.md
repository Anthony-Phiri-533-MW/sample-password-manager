This is the version of the code without a persistent key

##############################
import os
from cryptography.fernet import Fernet

def load_key():
    """Loads the key from a file, or generates a new one if not found."""
    if os.path.exists("key.txt"):
        with open("key.txt", "rb") as file:
            return file.read()
    else:
        key = Fernet.generate_key()
        with open("key.txt", "wb") as file:
            file.write(key)
        return key

key = load_key()
cipher_suite = Fernet(key)

# key = Fernet.generate_key()
# cipher_suite = Fernet(key)

def save_password(service, password):
    encrypted_password = cipher_suite.encrypt(password.encode())
    with open("password.txt", "a") as file:
        file.write(f"{service} : {encrypted_password.decode()}\n")

def get_password(service):
    with open("password.txt", "r") as file:
        for line in file:
            stored_service, stored_password = line.strip().split(" : ")
            if stored_service == service:
                return cipher_suite.decrypt(stored_password.encode()).decode()
            
def main():
    while True:
        action = input("would you like to 'Store' or 'Retrieve' a password?")
        if action == "Store":
            service = input("Enter the service name: ")
            password = input("Enter the password: ")
            save_password(service, password)
            print("password saved")
        elif action  == "Retrieve":
            service = input("Enter the service name: ")
            print("password", get_password(service))
        elif action == "Exit":
            break
        else:
            print("invalid option. Please choose 'Store', 'Retrieve' or 'Exit'. ")

if  __name__ == "__main__":
    main()