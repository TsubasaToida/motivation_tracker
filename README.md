At first, I was going to make an investment simulator that analyzes players' investment styles. However, I couldn't make it because of the lack of time. (Also, I wanted to use Streamlit.) Instead, I built this simulator for investment education. Based on the real-life economic events and assets, the assets in this simulator are supposed to change. For example, when COVID-19 happened, almost all assets like the S&P500 went down except Gold and Treasury Bonds. By playing this game, players could learn the basics of investment and how each asset works. As for detailed numbers, I wanted to make this simulation fun, so I doubled the volatilities and numbers for all assets. 

Those codes are mainly made from two classes and the main part.
Asset class holds information about each asset, such as its name, description, current price, first price, last month’s price, and the functions that update the price and calculate monthly and total percentage changes. The Player class holds players' cash and holdings, how players sell and buy assets, how to calculate invested value, and how to calculate total portfolio value.
At first, I tried to write this project without using classes. I was thinking about using dictionaries and separate variables to manage asset prices, player cash, and holdings. However, because I added more assets and more calculations later, the code became harder to organize, and it became really complicated. So I decided to use a class to organize it more. Using the class was difficult at first. Especially separating functions was really confusing. It took some time to understand what should belong to the asset class and what should belong to the player class. 

Overall, I think this game became a good learning experience for beginners in investment.


This is the Final Project that I wrote. "Make A Fortune"
--------

import random



#---------------------------------------
class Asset:
    def __init__(self, name, detail, price):
        self.name = name        
        self.detail = detail            
        self.price = price          
        self.first_price = price#the price before the game start
        self.last_price = price#the price in the last month
    def renew_price(self, total_change):#this is the method that renew the prices of each assets
        self.last_price = self.price#save the price in the last month to calculate % of change
        self.price = max(1,round(self.price * (1 + total_change), 2))#present price * total change
    def monthly_change(self):
        return ((self.price - self.last_price) / self.last_price) * 100
    def total_change(self):
        return ((self.price - self.first_price) / self.first_price) * 100



#---------------------------------------
class Player: 
    def __init__(self, start_cash):
        self.cash = start_cash               
        self.start_cash = start_cash      
        self.holdings = {}#hold assets in dictionary ex) {"S&P500": 12.5, "Gold": 3.0}
        self.capital_contributed = start_cash  #total cash invested until now
    def buybuybuy(self, asset, amount):
        if amount <= 0:
            print("Number must be greater than 0.")
            return False
        if amount > self.cash:
            print("Oops... you don't have enough cash.")
            return False
        shares = amount / asset.price#invest $1000 on $100 assets = 100 shares
        self.cash -= amount
        self.holdings[asset.name] = self.holdings.get(asset.name, 0) + shares#first buy doesn't cause error
        print(f"Bought {shares:.2f} shares of {asset.name}.")
        return True
    def sellsellsell(self, asset, amount):
        if amount <= 0:
            print("Amount must be greater than 0.")
            return False
        owned_shares = self.holdings.get(asset.name, 0)
        total_value = owned_shares * asset.price#share * present price
        if owned_shares <= 0:
            print(f"Oops... you do not own any {asset.name}.")
            return False
        if amount > total_value:
            print(f"Oops... you only have {money(total_value)} worth of {asset.name}.")
            return False
        sell_share = amount / asset.price#sell $500 of $100 assets = sell 5 shares
        self.holdings[asset.name] -= sell_share#lower the number of shares
        self.cash += amount
        print(f"Sold {sell_share:.2f} shares of {asset.name}.")
        return True
    def invested_value(self, assets):#calculate total value of assets invested (cash is excluded)
        total = 0
        for name, shares in self.holdings.items():
            total += shares * assets[name].price
        return round(total, 2)
    def total_value(self, assets):#include cash and assets invested
        return round(self.cash + self.invested_value(assets), 2)


#---------------------------------------
#support functions

def trend(value):
    if value > 0:
        return f"▲ {value:+.2f}%"#increasind
    elif value < 0:
        return f"▼ {value:+.2f}%"#decreasing
    else:
        return f"• {value:+.2f}%"

def money(x):
    return f"${x:,.2f}"#$1,234.56 until second decimal point



            
#---------------------------------------

def main():
    #initialize all assets 
    assets = {
        "S&P500":         Asset("S&P500", "Aeets that gather top 500 campanies in the US", 100.0),
        "All Country":    Asset("All Country", "Assets that gather top campanies globally including America, China, Japan, India, and Europeian countries etc.", 100.0),
        "Semiconductor":  Asset("Semiconductor", "Assets that gather top semiconductor campanies such as NVIDIA", 100.0),
        "Gold":           Asset("Gold", "Assets that move depending on the price of GOLD", 100.0),
        "Treasury Bonds": Asset("Treasury Bonds", "Long-term Treasury Bonds", 100.0),
        "Real Estate":    Asset("Real Estate", "Assets that move depending on the price of land and housing", 100.0),
        "Financials":     Asset("Financials", "Assets that gather main bank companies such as Bank Of America", 100.0),
        "Energy":         Asset("Energy", "Assets that gather energy related campanies like ExxonMobil", 100.0),
    }
    #set the details for all monthly events
    events = [
        {
            "name": "Inflation and High Interest Rate",
            "description": "Because of Intense Inflation, Government Bank Raised Interest Rate a Lot",
            "effects": {"S&P500": -0.04,"All Country": -0.03,"Semiconductor": -0.08,"Gold": -0.01,"Treasury Bonds": -0.08,"Real Estate": -0.06,"Financials": 0.06,"Energy": 0.01}
        },
        {
            "name": "AI Boom",
            "description": "The Demand of AI is Rapidly Increasing",
            "effects": {"S&P500": 0.02,"All Country": 0.01,"Semiconductor": 0.12,"Gold": -0.01,"Treasury Bonds": -0.01,"Real Estate": 0.01,"Financials": 0.02,"Energy": 0.01}
        },
        {
            "name": "Huge Recession",
            "description": "Because of COVID-19, Consumption Declined a Lot and People Are Losing Their Jobs",
            "effects": {"S&P500": -0.08,"All Country": -0.07,"Semiconductor": -0.12,"Gold": 0.04,"Treasury Bonds": 0.06,"Real Estate": -0.07,"Financials": -0.10,"Energy": -0.08}
        },
        {
            "name": "Geopolitical Risk",
            "description": "Political Tension Among Countries is Increasing",
            "effects": {"S&P500": -0.05,"All Country": -0.04,"Semiconductor": -0.08,"Gold": 0.10,"Treasury Bonds": 0.03,"Real Estate": -0.02,"Financials": -0.05,"Energy": 0.08}
        },
        {
            "name": "Happy Month",
            "description": "No Major Event in This Month",
            "effects": {"S&P500": 0.035,"All Country": 0.03,"Semiconductor": 0.075,"Gold": 0.025,"Treasury Bonds": 0.02,"Real Estate": 0.05,"Financials": 0.045,"Energy": 0.055}
        }
    ]
    #default setting
    player = Player(start_cash=5000)
    salary  = 3000  
    expense = 2000
    months = 12             
    print("-" * 60)
    print("Welcome to Investment Strategy Simulator!")
    print("Manage your portfolio for 12 months and double your assets!!!")
    print("For eash month, each assets will change depend on specific event.")
    print("You can check next monthly event at the bigging of every month.")
    print("-" * 60)
    print("           +                +")
    print("          /\\              /\\")
    print("         /  \\            /  \\")
    print("        |      ------------   ---|")
    print("        |                        |        ------------------------------")
    print("        |                        |      /                              |")
    print("        |      --         --     |    /                                |")
    print("        |                        |  /            GOOD LUCK GUYS        |")
    print("        |                        |  \\                                  |")
    print("        |            \ /         |    \\                                |")
    print("        |                        |      \\                              |")
    print("        |           ______       |        ------------------------------")
    print("        |                        |")
    print("        |________________________|")
    #game starts!!
    for month in range(1, months + 1):
        print("")
        print("")
        print("")
        print("")
        print("")
        print("")
        print("")
        print("")
        print("")
        print(f"MONTH{month}")
        #player get cash
        net_cashflow = salary - expense
        player.cash += net_cashflow
        player.capital_contributed += net_cashflow
        print(f"Salary: {money(salary)}  |  Expense: {money(expense)}  |  Net Income: {money(net_cashflow)}")
        print(f"Available cash: {money(player.cash)}")
        #monthly random events
        i = random.randint(1, 100)
        if i <= 20:
            event = events[0]#20%
        elif i <= 35:
            event = events[1]#15%
        elif i <= 45:
            event = events[2]#10%
        elif i <= 55:
            event = events[3]#10%
        else:
            event = events[4]#45%
        print(f"Next Monthly Event: {event['name']}")
        print(f"{event['description']}")
        #show assets (increased? or decreased?)
        print("")
        print("")
        print("--- Available Assets ---")
        i = 1
        for asset in assets.values():
            print(
            f"{i}. {asset.name:<18} | "
            f"Price: {money(asset.price):>10} | "
            f"Month: {trend(asset.monthly_change()):>10} | "
            f"Total: {trend(asset.total_change()):>10} | "
            f"{asset.detail}"
            )
            i += 1
        #show portfolio
        print("")
        print("")
        print("--- Portfolio ---")
        print(f"Cash:            {money(player.cash)}")
        print(f"Invested Assets: {money(player.invested_value(assets))}")
        print(f"Total Value:     {money(player.total_value(assets))}")
        has_asset = False
        for name, shares in player.holdings.items():
            if shares > 0:
                asset = assets[name]
                value = shares * asset.price
                print(
                    f"  {name:<18} | Shares: {shares:>8.2f} | "
                    f"Value: {money(value):>10} | "
                    f"Month: {trend(asset.monthly_change()):>10} | "
                    f"Total: {trend(asset.total_change()):>10}"
                )
                has_asset = True
        if not has_asset:
            print("No assets held yet.")
        #player's action
        while True:
            print("")
            print("")
            print("What would you like to do?")
            print("1. Buy")
            print("2. Sell")
            print("3. View portfolio")
            print("4. End month")
            choice = input("Enter your choice: ").strip() #delet amy spaces
            if choice == "1":
                print("")
                print("--- Available Assets ---")
                i = 1
                for asset in assets.values():
                    print(f"{i}. {asset.name:<18} | Price: {money(asset.price):>10} | {asset.detail}")
                    i += 1
                try: #prevent errors when player input anything other than whole number
                    num = int(input("Which asset number do you want to buy? "))
                    asset_name = list(assets.keys())[num - 1] #assets,key = S&P500, num-1 is neccesary because assets are showed from number 1
                    amount = float(input("How much do you want to invest? $"))
                    player.buybuybuy(assets[asset_name], amount)
                except (ValueError, IndexError):
                    print("Sorry that's invalid. Try again.")
            elif choice == "2":
                print("")
                print("--- Available Assets ---")
                i = 1
                for asset in assets.values():
                    print(f"{i}. {asset.name:<18} | Price: {money(asset.price):>10} | {asset.detail}")
                    i += 1
                try:
                    num = int(input("Which asset number do you want to sell? "))
                    asset_name = list(assets.keys())[num - 1]
                    amount = float(input("How much do you want to sell? $"))
                    player.sellsellsell(assets[asset_name], amount)
                except (ValueError, IndexError):
                    print("Sorry that's invalid. Try again.")
            elif choice == "3":
                print("")
                print("--- Portfolio ---")
                print(f"Cash:            {money(player.cash)}")
                print(f"Invested Assets: {money(player.invested_value(assets))}")
                print(f"Total Value:     {money(player.total_value(assets))}")
                for name, shares in player.holdings.items():
                    if shares > 0:
                        asset = assets[name]
                        print(f"  {name:<18} | Shares: {shares:>8.2f} | Value: {money(shares * asset.price):>10} | "
                              f"Month: {trend(asset.monthly_change()):>10} | Total: {trend(asset.total_change()):>10}")
            elif choice == "4":
                break
            else:
                print("Sorry that's invalid. Enter 1 to 4.")
        #renew the price at the end of mouth
        for name in assets:
            asset = assets[name]
            if event["name"] == "Happy Month":
                 change = event["effects"][name] + random.uniform(-0.04, 0.01)
            else:
                change = event["effects"][name]
            asset.renew_price(change)
        happiness = player.total_value(assets) - player.capital_contributed
        happiness_per = (happiness / player.capital_contributed) * 100
        print("")
        print("")
        print(f"--- End of Month {month} Summary ---")
        print(f"Cash:            {money(player.cash)}")
        print(f"Invested Assets: {money(player.invested_value(assets))}")
        print(f"Total Value:     {money(player.total_value(assets))}")
        print(f"Total Gain/Loss: ${happiness:+,.2f}")
        print(f"Total Return:    {trend(happiness_per)}")
    #final results for 12 month sumulation
    income_come = player.total_value(assets) - player.capital_contributed
    return_finally = (total_income / player.capital_contributed) * 100
    print("           +                +")
    print("          /\\              /\\")
    print("         /  \\            /  \\")
    print("        |      ------------   ---|")
    print("        |                        |        ------------------------------")
    print("        |                        |      /                              |")
    print("        |      --         --     |    /                                |")
    print("        |                        |  /            GOOD JOBBBBBB         |")
    print("        |                        |  \\                                  |")
    print("        |            \ /         |    \\                                |")
    print("        |                        |      \\                              |")
    print("        |           ______       |        ------------------------------")
    print("        |                        |")
    print("        |________________________|")
    print("")
    print("")
    print("===============================")
    print("FINAL RESULT")
    print("===============================")
    print(f"Final Portfolio Value: {money(player.total_value(assets))}")
    print(f"Total Gain/Loss: ${income_come:+,.2f}")
    print(f"Total Return:    {trend(return_finally)}")
    if player.total_value(assets) >= player.capital_contributed * 1.5: 
        print("Great Job! You grew your portfolio by more than 50%!")
    elif player.total_value(assets) >= player.capital_contributed:
        print("Well done! You managed to grow your assets.")
    else:
        print("Good luck for the next time! Try different strategies.")


if __name__ == "__main__":
    main()

