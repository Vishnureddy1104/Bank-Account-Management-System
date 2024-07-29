# Bank-Account-Management-System
from datetime import datetime

class BankAccount:
    def __init__(self, account_number, account_holder, balance=0):
        self.account_number = account_number
        self.account_holder = account_holder
        self.balance = balance
        self.transactions = []
        self.daily_withdrawal_limit = 10000
        self.daily_withdrawal_amount = 0
        self.last_withdrawal_date = datetime.now().date()

    def deposit(self, amount):
        if amount > 0:
            self.balance += amount
            self.transactions.append((datetime.now(), "Deposit", amount, self.balance))
            print(f"Deposited Amount is {amount}. New balance is {self.balance}")
        else:
            print("Deposit Amount should be Positive")

    def withdraw(self, amount):
        today = datetime.now().date()
        if self.last_withdrawal_date != today:
            self.daily_withdrawal_amount = 0
            self.last_withdrawal_date = today

        if 0 < amount <= self.balance and self.daily_withdrawal_amount + amount <= self.daily_withdrawal_limit:
            self.balance -= amount
            self.daily_withdrawal_amount += amount
            self.transactions.append((datetime.now(), "Withdrawal", amount, self.balance))
            print(f"Withdrawal Amount is {amount}. New balance is {self.balance}")
        else:
            print("Insufficient funds or Daily withdrawal limit reached")

    def check_balance(self):
        print(f"The Current balance is {self.balance}")

    def print_transaction_history(self):
        for transaction in self.transactions:
            print(f"Date: {transaction[0]}, Type: {transaction[1]}, Amount: {transaction[2]}, Balance: {transaction[3]}")

    def generate_statement(self, start_date, end_date):
        print(f"Statement from {start_date} to {end_date}:")
        for transaction in self.transactions:
            if start_date <= transaction[0].date() <= end_date:
                print(f"Date: {transaction[0]}, Type: {transaction[1]}, Amount: {transaction[2]}, Balance: {transaction[3]}")

class SavingsAccount(BankAccount):
    def __init__(self, account_number, account_holder, balance=0, interest_rate=0.02):
        super().__init__(account_number, account_holder, balance)
        self.interest_rate = interest_rate

    def apply_interest(self):
        interest = self.balance * self.interest_rate
        self.balance += interest
        self.transactions.append((datetime.now(), "Interest", interest, self.balance))
        print(f"Interest is {interest}. Current balance is {self.balance}")

class CheckingAccount(BankAccount):
    def __init__(self, account_number, account_holder, balance=0, overdraft_limit=1000):
        super().__init__(account_number, account_holder, balance)
        self.overdraft_limit = overdraft_limit

    def withdraw(self, amount):
        today = datetime.now().date()
        if self.last_withdrawal_date != today:
            self.daily_withdrawal_amount = 0
            self.last_withdrawal_date = today

        if 0 < amount <= (self.balance + self.overdraft_limit) and self.daily_withdrawal_amount + amount <= self.daily_withdrawal_limit:
            self.balance -= amount
            self.daily_withdrawal_amount += amount
            self.transactions.append((datetime.now(), "Withdrawal", amount, self.balance))
            print(f"Withdrew {amount}. The Current balance is {self.balance}")
        else:
            print("Insufficient funds or Invalid amount")

def transfer(from_account, to_account, amount):
    if isinstance(from_account, CheckingAccount) and (from_account.balance + from_account.overdraft_limit) >= amount:
        from_account.withdraw(amount)
        to_account.deposit(amount)
        print(f"Transferred {amount} from {from_account.account_number} to {to_account.account_number}")
    elif isinstance(from_account, BankAccount) and from_account.balance >= amount:
        from_account.withdraw(amount)
        to_account.deposit(amount)
        print(f"Transferred {amount} from {from_account.account_number} to {to_account.account_number}")
    else:
        print("Insufficient funds for transfer.")

# Test the classes
A = BankAccount("900700123", "John", 50000)
B = BankAccount("900700125", "Beck", 70000)
C = BankAccount("900700129", "Finn", 40000)

A.deposit(10000)
A.withdraw(5000)
A.check_balance()
A.print_transaction_history()
start_date = datetime(2024, 1, 1).date()
end_date = datetime(2024, 12, 1).date()
A.generate_statement(start_date, end_date)

A1 = SavingsAccount("900800123", "John", 20000)
A1.apply_interest()

A2 = CheckingAccount("900900123", "John", 50000)
A2.withdraw(5500)
A2.check_balance()
A2.print_transaction_history()

print("----------------------------------------------------------------------------------")
B.deposit(10000)
B.withdraw(5000)
B.check_balance()
B.print_transaction_history()
start_date = datetime(2024, 1, 1).date()
end_date = datetime(2024, 12, 1).date()
B.generate_statement(start_date, end_date)

B1 = SavingsAccount("900800125", "John", 25000)
B1.apply_interest()

B2 = CheckingAccount("900900125", "John", 40000)
B2.withdraw(6500)
B2.check_balance()
B2.print_transaction_history()

print("----------------------------------------------------------------------------------")
C.deposit(10000)
C.withdraw(5000)
C.check_balance()
C.print_transaction_history()
start_date = datetime(2024, 1, 1).date()
end_date = datetime(2024, 12, 1).date()
C.generate_statement(start_date, end_date)

C1 = SavingsAccount("900800129", "John", 55000)
C1.apply_interest()

C2 = CheckingAccount("900900129", "John", 90000)
C2.withdraw(9500)
C2.check_balance()
C2.print_transaction_history()
