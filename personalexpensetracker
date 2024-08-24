from pymongo import MongoClient
from datetime import datetime
from bson.objectid import ObjectId

class Expense:
    def __init__(self, description, amount, date):
        self.description = description
        self.amount = amount
        self.date = date

class ExpenseTracker:
    def __init__(self, db_name='expense_tracker', collection_name='expenses'):
        self.client = MongoClient('mongodb://localhost:27017/')
        self.db = self.client[db_name]
        self.collection = self.db[collection_name]

    def add_expense(self, expense):
        if expense.amount <= 0:
            print("Amount should be a positive number.")
            return
        try:
            datetime.strptime(expense.date, '%Y-%m-%d')
        except ValueError:
            print("Date format should be YYYY-MM-DD.")
            return
        expense_doc = {
            'description': expense.description,
            'amount': expense.amount,
            'date': expense.date
        }
        result = self.collection.insert_one(expense_doc)
        print(f"Expense added with ID: {result.inserted_id}")

    def view_expenses(self):
        expenses = self.collection.find()
        if self.collection.count_documents({}) == 0:
            print("No expenses recorded.")
            return
        for expense in expenses:
            print(f"ID: {expense['_id']}, Description: {expense['description']}, Amount: {expense['amount']}, Date: {expense['date']}")

    def view_total_expenses(self):
        total = self.collection.aggregate([
            {'$group': {'_id': None, 'total': {'$sum': '$amount'}}}
        ])
        total_amount = list(total)
        if total_amount:
            total_amount = total_amount[0]['total']
        else:
            total_amount = 0
        print(f"Total Expenses: ${total_amount:.2f}")

    def delete_expense(self, expense_id):
        try:
            expense_id = ObjectId(expense_id)
        except Exception as e:
            print(f"Invalid ID: {e}")
            return
        result = self.collection.delete_one({'_id': expense_id})
        if result.deleted_count == 1:
            print("Expense deleted successfully.")
        else:
            print("Expense not found.")

def display_menu():
    print("\nPersonal Expense Tracker")
    print("1. Add New Expense")
    print("2. View All Expenses")
    print("3. View Total Expenses")
    print("4. Delete an Expense")
    print("5. Exit")

def main():
    tracker = ExpenseTracker()
    while True:
        display_menu()
        choice = input("Enter your choice (1-5): ")
        if choice == '1':
            description = input("Enter description: ")
            amount = float(input("Enter amount: "))
            date = input("Enter date (YYYY-MM-DD): ")
            expense = Expense(description, amount, date)
            tracker.add_expense(expense)
        elif choice == '2':
            tracker.view_expenses()
        elif choice == '3':
            tracker.view_total_expenses()
        elif choice == '4':
            expense_id = input("Enter expense ID to delete: ")
            tracker.delete_expense(expense_id)
        elif choice == '5':
            print("Exiting...")
            break
        else:
            print("Invalid choice. Please enter a number between 1 and 5.")

if __name__ == "__main__":
    main()
