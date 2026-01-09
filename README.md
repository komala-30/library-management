# library-management
A **Library Management System** is a Python-based mini project that helps manage library operations such as adding, viewing, issuing, and returning books. It uses an SQLite database to store book records permanently and simplifies manual library management tasks.
import sqlite3

def connect_db():
    return sqlite3.connect("library.db")

def create_table():
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("""
    CREATE TABLE IF NOT EXISTS books (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        title TEXT NOT NULL,
        author TEXT NOT NULL,
        quantity INTEGER NOT NULL
    )
    """)
    conn.commit()
    conn.close()
from database import connect_db, create_table

def add_book():
    title = input("Enter book title: ")
    author = input("Enter author name: ")
    quantity = int(input("Enter quantity: "))

    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("INSERT INTO books (title, author, quantity) VALUES (?, ?, ?)",
                   (title, author, quantity))
    conn.commit()
    conn.close()
    print("✅ Book added successfully!")

def view_books():
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM books")
    books = cursor.fetchall()
    conn.close()

    print("\n📚 Available Books:")
    for book in books:
        print(book)

def issue_book():
    book_id = int(input("Enter book ID to issue: "))
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("SELECT quantity FROM books WHERE id=?", (book_id,))
    result = cursor.fetchone()

    if result and result[0] > 0:
        cursor.execute("UPDATE books SET quantity = quantity - 1 WHERE id=?", (book_id,))
        conn.commit()
        print("📖 Book issued successfully!")
    else:
        print("❌ Book not available")

    conn.close()

def return_book():
    book_id = int(input("Enter book ID to return: "))
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("UPDATE books SET quantity = quantity + 1 WHERE id=?", (book_id,))
    conn.commit()
    conn.close()
    print("🔄 Book returned successfully!")

def delete_book():
    book_id = int(input("Enter book ID to delete: "))
    conn = connect_db()
    cursor = conn.cursor()
    cursor.execute("DELETE FROM books WHERE id=?", (book_id,))
    conn.commit()
    conn.close()
    print("🗑️ Book deleted successfully!")

def menu():
    create_table()
    while True:
        print("\n===== Library Management System =====")
        print("1. Add Book")
        print("2. View Books")
        print("3. Issue Book")
        print("4. Return Book")
        print("5. Delete Book")
        print("6. Exit")

        choice = input("Enter choice: ")

        if choice == "1":
            add_book()
        elif choice == "2":
            view_books()
        elif choice == "3":
            issue_book()
        elif choice == "4":
            return_book()
        elif choice == "5":
            delete_book()
        elif choice == "6":
            print("👋 Exiting...")
            break
        else:
            print("❌ Invalid choice")

menu()
sqlite3
