import json
import hashlib
from pathlib import Path
from getpass import getpass

DB_FILE = Path("vault.json")


class SecretVault:

    def __init__(selfF):
        if not DB_FILE.exists():
            DB_FILE.write_text("{}")

    def load(self):
        with open(DB_FILE, "r", encoding="utf-8") as f:
            return json.load(f)

    def save(self, data):
        with open(DB_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=4)

    def hash_password(self, password):
        return hashlib.sha256(
            password.encode()
        ).hexdigest()

    def add_secret(self):

        title = input("Title: ")

        secret = input("Secret Message: ")

        password = getpass(
            "Password: "
        )

        data = self.load()

        data[title] = {
            "password": self.hash_password(
                password
            ),
            "message": secret
        }

        self.save(data)

        print("Secret saved.")

    def read_secret(self):

        title = input("Title: ")

        data = self.load()

        if title not in data:
            print("Secret not found.")
            return

        password = getpass(
            "Password: "
        )

        hashed = self.hash_password(
            password
        )

        if hashed == data[title]["password"]:

            print("\nSecret:")
            print(
                data[title]["message"]
            )

        else:
            print("Wrong password.")

    def list_titles(self):

        data = self.load()

        print("\nStored Secrets\n")

        for title in data:
            print("-", title)


def main():

    vault = SecretVault()

    while True:

        print("\n=== Secret Vault ===")
        print("1. Add Secret")
        print("2. Read Secret")
        print("3. List Secrets")
        print("4. Exit")

        choice = input(
            "\nSelect: "
        )

        if choice == "1":
            vault.add_secret()

        elif choice == "2":
            vault.read_secret()

        elif choice == "3":
            vault.list_titles()

        elif choice == "4":
            break

        else:
            print("Invalid choice")


if __name__ == "__main__":
    main()
