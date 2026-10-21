import random
import time

class SupermarketSimulator:
    def __init__(self):
        self.money = 1000.00
        self.day = 1
        self.inventory = {
            "Bread": {"stock": 10, "cost": 1.20, "price": 2.50},
            "Milk":  {"stock": 8,  "cost": 1.50, "price": 3.20},
            "Eggs":  {"stock": 12, "cost": 2.00, "price": 4.00},
            "Cereal":{"stock": 5,  "cost": 3.00, "price": 5.50}
        }
        self.log = []

    def display_status(self):
        print(f"\n=================== DAY {self.day} ===================")
        print(f"Store Balance: ${self.money:.2f}")
        print("------------------------------------------------")
        print(f"{'Product':<10} | {'Stock':<6} | {'Cost Price':<10} | {'Your Price':<10}")
        print("------------------------------------------------")
        for item, info in self.inventory.items():
            print(f"{item:<10} | {info['stock']:<6} | ${info['cost']:<9.2f} | ${info['price']:<9.2f}")
        print("================================================")

    def order_stock(self):
        print("\n--- Wholesaler Market ---")
        for i, (item, info) in enumerate(self.inventory.items(), 1):
            print(f"{i}. {item} (Cost: ${info['cost']:.2f})")
        
        try:
            choice = int(input("Select product number to buy (0 to cancel): "))
            if choice == 0: return
            item_name = list(self.inventory.keys())[choice - 1]
            amount = int(input(f"How many units of {item_name} do you want to order? "))
            
            total_cost = self.inventory[item_name]['cost'] * amount
            if total_cost > self.money:
                print("❌ Insufficient funds!")
            else:
                self.money -= total_cost
                self.inventory[item_name]['stock'] += amount
                print(f"📦 Successfully stocked {amount}x {item_name} for ${total_cost:.2f}")
        except (ValueError, IndexError):
            print("❌ Invalid input.")

    def adjust_prices(self):
        print("\n--- Pricing Management ---")
        for i, item in enumerate(self.inventory.keys(), 1):
            print(f"{i}. {item} (Current Price: ${self.inventory[item]['price']:.2f})")
            
        try:
            choice = int(input("Select product number to reprice (0 to cancel): "))
            if choice == 0: return
            item_name = list(self.inventory.keys())[choice - 1]
            new_price = float(input(f"Set new selling price for {item_name}: $"))
            
            if new_price <= 0:
                print("❌ Price must be positive!")
            else:
                self.inventory[item_name]['price'] = new_price
                print(f"🏷️ {item_name} price updated to ${new_price:.2f}")
        except (ValueError, IndexError):
            print("❌ Invalid input.")

    def open_store(self):
        print("\n🔔 Store is now OPEN! Customers are walking in...")
        time.sleep(1)
        
        customers = random.randint(3, 7)
        daily_revenue = 0
        items_sold = 0
        
        for c in range(1, customers + 1):
            print(f"\n👤 Customer #{c} is browsing...")
            time.sleep(0.5)
            
            # Customer decides what to buy
            for item, info in self.inventory.items():
                if info['stock'] > 0:
                    # Customers complain or refuse to buy if markup is too high
                    max_acceptable = info['cost'] * 2.2 
                    if info['price'] > max_acceptable:
                        print(f"  - Refused to buy {item}: 'Too expensive!'")
                        continue
                        
                    if random.choice([True, False]): # 50% chance to want the item
                        buy_qty = random.randint(1, min(3, info['stock']))
                        info['stock'] -= buy_qty
                        revenue = info['price'] * buy_qty
                        daily_revenue += revenue
                        items_sold += buy_qty
                        print(f"  + Bought {buy_qty}x {item} for ${revenue:.2f}")
                        
        self.money += daily_revenue
        print("\n------------------------------------------------")
        print(f"🔔 Store CLOSED. Daily Summary:")
        print(f"Total items sold: {items_sold}")
        print(f"Revenue collected: ${daily_revenue:.2f}")
        self.day += 1
        input("\nPress Enter to start the next day...")

    def run(self):
        while True:
            self.display_status()
            print("What would you like to do?")
            print("1. Order Stock (Wholesaler)")
            print("2. Adjust Item Prices")
            print("3. Open Store for the Day")
            print("4. Exit Game")
            
            choice = input("Enter choice (1-4): ")
            if choice == '1':
                self.order_stock()
            elif choice == '2':
                self.adjust_prices()
            elif choice == '3':
                self.open_store()
            elif choice == '4':
                print("Thanks for playing Supermarket Simulator!")
                break
            else:
                print("Invalid choice.")

if __name__ == "__main__":
    game = SupermarketSimulator()
    game.run()
