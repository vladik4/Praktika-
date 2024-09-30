import json
import datetime
import matplotlib.pyplot as plt

# Структура даних для збереження доходів та витрат
data = {
    "income": [],
    "expenses": {
        "food": [],
        "transport": [],
        "entertainment": [],
        "other": []
    }
}

# Функція для додавання доходу
def add_income(amount):
    data["income"].append({
        "amount": amount,
        "date": str(datetime.date.today())
    })
    print(f"Дохід {amount} додано.")

# Функція для додавання витрати
def add_expense(category, amount):
    if category not in data["expenses"]:
        print(f"Категорія {category} не існує.")
        return
    data["expenses"][category].append({
        "amount": amount,
        "date": str(datetime.date.today())
    })
    print(f"Витрата {amount} додана до категорії {category}.")

# Функція для відображення витрат за категоріями
def display_expenses_by_category():
    print("\nВитрати за категоріями:")
    for category, expenses in data["expenses"].items():
        total = sum(expense["amount"] for expense in expenses)
        print(f"{category.capitalize()}: {total} грн")

# Функція для візуалізації витрат
def visualize_expenses():
    categories = []
    totals = []

    for category, expenses in data["expenses"].items():
        total = sum(expense["amount"] for expense in expenses)
        if total > 0:
            categories.append(category)
            totals.append(total)

    plt.figure(figsize=(8, 6))
    plt.pie(totals, labels=categories, autopct='%1.1f%%')
    plt.title("Витрати за категоріями")
    plt.show()

# Функція для збереження даних у файл
def save_data():
    with open("finance_data.json", "w") as f:
        json.dump(data, f)
    print("Дані збережено у файл.")

# Функція для завантаження даних з файлу
def load_data():
    global data
    try:
        with open("finance_data.json", "r") as f:
            data = json.load(f)
        print("Дані успішно завантажено.")
    except FileNotFoundError:
        print("Файл даних не знайдено. Створено новий файл.")

# Функція для прогнозу витрат
def predict_future_expenses():
    total_expenses = 0
    days_recorded = 0
    today = datetime.date.today()

    for category, expenses in data["expenses"].items():
        for expense in expenses:
            expense_date = datetime.datetime.strptime(expense["date"], "%Y-%m-%d").date()
            if expense_date.month == today.month and expense_date.year == today.year:
                total_expenses += expense["amount"]
                days_recorded += 1

    if days_recorded == 0:
        print("Недостатньо даних для прогнозу.")
        return

    avg_daily_expense = total_expenses / days_recorded
    remaining_days = (today.replace(month=today.month+1, day=1) - today).days
    projected_expenses = avg_daily_expense * remaining_days

    print(f"\nСередньоденні витрати: {avg_daily_expense:.2f} грн")
    print(f"Прогнозовані витрати до кінця місяця: {projected_expenses:.2f} грн")

# Головне меню
def main_menu():
    load_data()
    
    while True:
        print("\n--- Розумний фінансовий помічник ---")
        print("1. Додати дохід")
        print("2. Додати витрату")
        print("3. Показати витрати за категоріями")
        print("4. Візуалізувати витрати")
        print("5. Прогноз майбутніх витрат")
        print("6. Зберегти дані")
        print("7. Вийти")

        choice = input("Оберіть дію: ")

        if choice == "1":
            amount = float(input("Введіть суму доходу: "))
            add_income(amount)
        elif choice == "2":
            category = input("Введіть категорію (food, transport, entertainment, other): ")
            amount = float(input("Введіть суму витрати: "))
            add_expense(category, amount)
        elif choice == "3":
            display_expenses_by_category()
        elif choice == "4":
            visualize_expenses()
        elif choice == "5":
            predict_future_expenses()
        elif choice == "6":
            save_data()
        elif choice == "7":
            save_data()
            print("До побачення!")
            break
        else:
            print("Неправильний вибір, спробуйте ще раз.")

# Запуск програми
if __name__ == "__main__":
    main_menu()
