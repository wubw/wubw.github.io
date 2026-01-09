+++
author = "Binwei Wu"
title = "Thoughts on Vibe Coding"
date = "2026-01-09"
description = "Thoughts on Vibe Coding"
featured = true
tags = [
    "ai"
]
categories = [
    "Engineering",
]
series = "2026"
aliases = ["migrate-from-jekyl"]

+++

I recently used vibe coding to complete two prototypes for my side projects. The experience was fantastic!

# General Experience

I primarily used Visual Studio Code’s Agent mode with the Claude Sonnet 4.5 model. I would describe my requirements to the Agent, and it would write the code for me. I was impressed by how much it could accomplish in a short time, building business logic, user interfaces, and even unit tests. The experience felt like having a junior engineer working for me, though I needed to be clear and precise with my requirements.

Sometimes, the Agent would produce incorrect code, requiring me to review and refine my requirements or break them into smaller, more digestible pieces. In rare cases, the AI failed to follow instructions entirely, forcing me to start over or switch direction. 
The speed of building new features was remarkable. I’d estimate it was 2x to 3x faster than doing everything myself.

# Drawbacks and Risks

After experimenting for a while, I also noticed some drawbacks and risks associated with vibe coding, especially for production use.

**Code Quality**​

When receiving auto-generated code, the process moved so quickly that I often didn’t bother to review it, simply enjoying the new functionality. As a result, much of the code was outside my understanding and control. This became problematic when performance tuning or system optimization was needed—I struggled to identify bottlenecks. If a tricky bug arose that the AI couldn’t resolve, tracking down the root cause became a nightmare.

**Regression​**

As the repository grew, I noticed that the AI was more likely to introduce regression bugs in existing functionality. You might ask, “But don’t unit tests protect against that?” Unfortunately, I hadn’t reviewed most of the auto-generated unit tests, so when a regression occurred—whether it broke tests or not—I had to rely on the AI to diagnose and fix the tests. This made it hard to judge the quality of the test suite, and my confidence in the system diminished as it scaled.

# Lessons Learned So Far​

Although I’m new to vibe coding, I’ve already gained valuable insights. If you plan to use AI-generated code in production, thorough code review is essential, especially for core components. This helps strike the right balance between development speed and code quality.

Specification-Driven Development also shows promise. By maintaining a specification file and asking the AI to generate code and tests from it, you create a clear record of requirements. This approach can reduce the risk of regression as the system grows and helps build confidence in its quality.

For instance, I experimented with building a concept for a “Lot” (a stock investment term) and started with a specification file: Lot.md. You can refer to the generated code and unit tests for more details.


```
Implement a python class called **Lot**, which inherit the Pool class. 

# Functional Requirement

It implements the virtual functions in Pool class:
- buy: buy stock, the parameters include stock name, date, number of stock, stock price and commission. return true if the function succeeds else false
- sell: sell stock, the parameters include stock name, date, number of stock, stock price and commission. return true if the function succeeds else false
- get_total_cost: return number of stock * stock price + commission
- get_remaining_cost: remaining number of stock * buy stock price + income commission
- get_value: remaining number of stock * buy stock price
- get_unrealized_profit: value - remainingCost
- get_realized_profit: dividend + sold stock profit
- get_return: remaining stock value / remaining stock cost
- get_total_return: remaining stock value + unrealized profit / total cost
- get_isclosed: if no stock remained

- add_dividend: add dividend data, the parameters include stock name, date, and dividend per stock. return true if the function succeeds else false
- get_dividend: return dividend amount
- get_latestprice: get the latest price of the stock

## Restriction

There are several restriction needs to be followed:
- one lot object only allows one buy transaction, but multiple sell transactions 
- all the sell transactions need to be recorded in the lot object

# Technical Requirement

If the function fails, there should be error message logged elegantly. 
- The error message needs to include the class name and function name.
- Do not introduce any member variable to store the error message like self.last_error. 

# Test

Generate unit tests under test folder for the above functions

## test 1: create a lot
Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commision.

Check: 
- get_total_cost should be 10*350+1 = 3501
- get_remaining_cost should also be 3501
- get_value should be 3500
- get_unrealized_profit should be very small negative number
- get_realized_profit should be 0
- get_return should be very small negative number
- get_total_return should be very small negative number
- get_isclosed should be false


## test 2: add dividend
Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commision.
Add dividend with 6 USD per stock

Check: 
- get_value should be 3500
- get_unrealized_profit should be very small negative number
- get_realized_profit should be 10*6=60
- get_return should be very small negative number
- get_total_return should be a positive number

## test 3: sell stock
Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commision.
Sell 4 stock with 400 USD price which is also the latest price of the stoc, the commision is 0.5 USD

Check:
- get_value should be 6*400 = 2400
- get_unrealized_profit should be 2400-6*350-1 = 299
- get_realized_profit should be 4*50-0.5=199.5
- get_return should be around 14.286%
- get_total_return should be around 14.286%

## test 4: add dividend + sell stock
Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commision.
Add dividend with 6 USD per stock
Then latest price is 400 USD

Check:
- get_value should be 4000
- get_unrealized_profit 500
- get_realized_profit should be 10*6=60
- get_return should be 14.286%
- get_total_return should be (4000+60)/(3501)= 15.967%

Sell 4 stock with 450 USD price and the commission is 0.5 USD
The latest price is 500 USD

Check:
- get_value should be 3000
- get_unrealized_profit 900
- get_realized_profit should be 10*6 + 100*4 = 460
- get_return should be 42.857%
- get_total_return should be (3000+1800+60)/(3501)= 38.817%
```

Generated code:

```
from pool import Pool
from typing import Optional, List, Dict
from datetime import datetime
import logging

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)


class Lot(Pool):
    """
    Lot class represents a single purchase lot of stock.
    
    Tracks a specific stock purchase including buy/sell transactions,
    costs, dividends, and profits.
    
    Restrictions:
    - Only one buy transaction allowed per Lot
    - Multiple sell transactions are allowed
    - All sell transactions are recorded
    """

    def __init__(self):
        """Initialize an empty lot."""
        self.stock_name: Optional[str] = None
        self.buy_quantity: float = 0.0
        self.buy_price: float = 0.0
        self.buy_commission: float = 0.0
        self.remaining_quantity: float = 0.0
        self.latest_price: float = 0.0  # Track latest price from sell transactions
        self.total_dividends: float = 0.0
        self.realized_profit_value: float = 0.0
        self.sell_proceeds: float = 0.0  # Track total proceeds from sell transactions
        self.sell_transactions: List[Dict] = []  # Record all sell transactions
        self.dividend_records: List[Dict] = []  # Record all dividend transactions

    def buy(self, stock_name: str, quantity: float, price: float, commission: float = 0.0) -> bool:
        """
        Buy stock.
        
        RESTRICTION: A lot object only allows ONE buy transaction.
        Additional buy attempts will be rejected.
        
        Args:
            stock_name: Name of the stock
            quantity: Number of shares to buy
            price: Price per share
            commission: Commission fee for the transaction
            
        Returns:
            bool: True if successful, False otherwise
        """
        try:
            if not stock_name or not stock_name.strip():
                logger.error("Lot.buy: Stock name cannot be empty")
                return False
            
            if quantity <= 0:
                logger.error(f"Lot.buy: Invalid quantity {quantity}. Quantity must be positive")
                return False
            
            if price < 0:
                logger.error(f"Lot.buy: Invalid price {price}. Price cannot be negative")
                return False
            
            if commission < 0:
                logger.error(f"Lot.buy: Invalid commission {commission}. Commission cannot be negative")
                return False
            
            # RESTRICTION: Only one buy transaction allowed per Lot
            if self.stock_name is not None:
                logger.error(f"Lot.buy: Restriction violated. Lot already contains {self.stock_name}. Only one buy transaction allowed per Lot")
                return False
            
            # First and only purchase for this lot
            self.stock_name = stock_name
            self.buy_quantity = quantity
            self.buy_price = price
            self.buy_commission = commission
            self.remaining_quantity = quantity
            self.latest_price = price  # Initialize latest price with buy price
            logger.info(f"Lot.buy: Successfully bought {quantity} shares of {stock_name} at {price}")
            return True
            
        except Exception as e:
            logger.error(f"Lot.buy: Unexpected error during buy transaction: {str(e)}")
            return False

    def sell(self, stock_name: str, quantity: float, price: float, commission: float = 0.0) -> bool:
        """
        Sell stock.
        
        RESTRICTION: Multiple sell transactions are allowed (partial or full sales).
        All sell transactions are recorded.
        
        Args:
            stock_name: Name of the stock
            quantity: Number of shares to sell
            price: Price per share
            commission: Commission fee for the transaction
            
        Returns:
            bool: True if successful, False otherwise
        """
        try:
            if self.stock_name is None:
                logger.error("Lot.sell: Cannot sell from an empty lot. No stock has been purchased yet")
                return False
            
            if self.stock_name != stock_name:
                logger.error(f"Lot.sell: Stock mismatch. Lot contains {self.stock_name} but trying to sell {stock_name}")
                return False
            
            if quantity <= 0:
                logger.error(f"Lot.sell: Invalid quantity {quantity}. Quantity must be positive")
                return False
            
            if quantity > self.remaining_quantity:
                logger.error(f"Lot.sell: Insufficient shares. Trying to sell {quantity} shares but only {self.remaining_quantity} available")
                return False
            
            if price < 0:
                logger.error(f"Lot.sell: Invalid price {price}. Price cannot be negative")
                return False
            
            if commission < 0:
                logger.error(f"Lot.sell: Invalid commission {commission}. Commission cannot be negative")
                return False
            
            # Calculate profit from this sale
            # According to spec test 3: profit = (sell_price - buy_price) * quantity - commission
            profit = (price - self.buy_price) * quantity - commission
            
            # Track sell proceeds (for total_return calculation)
            proceeds = price * quantity - commission
            self.sell_proceeds += proceeds
            
            # Update realized profit
            self.realized_profit_value += profit
            
            # Update remaining quantity
            self.remaining_quantity -= quantity
            
            # Update latest price with the sell price
            self.latest_price = price
            
            # RESTRICTION: Record this sell transaction
            self.sell_transactions.append({
                'stock_name': stock_name,
                'quantity': quantity,
                'price': price,
                'commission': commission,
                'profit': profit,
                'timestamp': datetime.now()
            })
            
            logger.info(f"Lot.sell: Successfully sold {quantity} shares of {stock_name} at {price}. Profit: {profit:.2f}")
            return True
            
        except Exception as e:
            logger.error(f"Lot.sell: Unexpected error during sell transaction: {str(e)}")
            return False

    def add_dividend(self, stock_name: str, date: str, dividend_per_stock: float) -> bool:
        """
        Add dividend income to the lot.
        
        Args:
            stock_name: Name of the stock
            date: Date of dividend payment (YYYY-MM-DD format)
            dividend_per_stock: Dividend amount per share
            
        Returns:
            bool: True if successful, False otherwise
        """
        try:
            if self.stock_name is None:
                logger.error("Lot.add_dividend: Cannot add dividend to an empty lot. No stock has been purchased yet")
                return False
            
            if self.stock_name != stock_name:
                logger.error(f"Lot.add_dividend: Stock mismatch. Lot contains {self.stock_name} but trying to add dividend for {stock_name}")
                return False
            
            if not date or not date.strip():
                logger.error("Lot.add_dividend: Date cannot be empty")
                return False
            
            if dividend_per_stock < 0:
                logger.error(f"Lot.add_dividend: Invalid dividend amount {dividend_per_stock}. Amount cannot be negative")
                return False
            
            # Calculate total dividend for the lot based on buy quantity (original shares)
            total_dividend = dividend_per_stock * self.buy_quantity
            self.total_dividends += total_dividend
            
            # Record this dividend transaction
            self.dividend_records.append({
                'stock_name': stock_name,
                'date': date,
                'dividend_per_stock': dividend_per_stock,
                'total_dividend': total_dividend,
                'shares_owned': self.buy_quantity
            })
            
            logger.info(f"Lot.add_dividend: Added dividend of {dividend_per_stock} per share on {date}. Total dividend: {total_dividend:.2f}. Cumulative dividends: {self.total_dividends:.2f}")
            return True
            
        except Exception as e:
            logger.error(f"Lot.add_dividend: Unexpected error: {str(e)}")
            return False

    def get_dividend(self) -> float:
        """
        Get the total dividend amount received.
        
        Returns:
            float: Total dividend amount
        """
        return self.total_dividends

    def get_total_cost(self) -> float:
        """
        Calculate total cost of the original purchase.
        According to spec: number of stock * stock price + commission
        
        Returns:
            float: Total cost (buy_quantity * buy_price + buy_commission)
        """
        return self.buy_quantity * self.buy_price + self.buy_commission

    def get_remaining_cost(self) -> float:
        """
        Calculate the cost basis of remaining shares.
        According to spec: remaining number of stock * buy stock price + income commission
        
        Returns:
            float: Cost of remaining shares
        """
        return self.remaining_quantity * self.buy_price + self.buy_commission

    def get_value(self) -> float:
        """
        Calculate current value of remaining shares.
        According to spec test 3: remaining number of stock * latest price
        
        Returns:
            float: Value based on latest price
        """
        return self.remaining_quantity * self.latest_price

    def get_unrealized_profit(self) -> float:
        """
        Calculate unrealized profit on remaining shares.
        According to spec: value - remainingCost
        
        Returns:
            float: Unrealized profit
        """
        return self.get_value() - self.get_remaining_cost()

    def get_realized_profit(self) -> float:
        """
        Calculate realized profit from dividends and sales.
        According to spec: dividend + sold stock profit
        
        Returns:
            float: Total realized profit
        """
        return self.total_dividends + self.realized_profit_value

    def get_return(self) -> float:
        """
        Calculate return rate on remaining investment.
        According to spec: remaining stock value / remaining stock cost
        
        Returns:
            float: Return rate
        """
        remaining_cost = self.get_remaining_cost()
        if remaining_cost == 0:
            return 0.0
        return self.get_value() / remaining_cost

    def get_total_return(self) -> float:
        """
        Calculate total return rate.
        According to spec test 4: (value + sell_proceeds + dividends) / total_cost
        This measures total wealth (remaining shares + cash from sales + dividends)
        relative to initial investment.
        
        Returns:
            float: Total return rate
        """
        total_cost = self.get_total_cost()
        if total_cost == 0:
            return 0.0
        return (self.get_value() + self.sell_proceeds + self.total_dividends) / total_cost

    def get_isclosed(self) -> bool:
        """
        Check if the lot is closed (no remaining shares).
        According to spec: if no stock remained
        
        Returns:
            bool: True if no shares remain, False otherwise
        """
        return self.remaining_quantity == 0

    def get_latestprice(self) -> float:
        """
        Get the latest price of the stock.
        This is updated when sell transactions occur.
        
        Returns:
            float: Latest price of the stock
        """
        return self.latest_price
```

Generated unit tests:

```
import unittest
import sys
import os
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '..', 'src')))

from lot import Lot


class TestLot(unittest.TestCase):
    """Test cases for Lot class"""

    def test_1_create_a_lot(self):
        """
        Test 1: Create a lot
        Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commission.
        """
        # Create lot and buy stock
        lot = Lot()
        result = lot.buy('AAPL', 10, 350, 1)
        
        # Verify buy was successful
        self.assertTrue(result, "Buy transaction should succeed")
        
        # Check get_total_cost should be 10*350+1 = 3501
        self.assertEqual(lot.get_total_cost(), 3501, "Total cost should be 3501")
        
        # Check get_remaining_cost should also be 3501
        self.assertEqual(lot.get_remaining_cost(), 3501, "Remaining cost should be 3501")
        
        # Check get_value should be 3500
        self.assertEqual(lot.get_value(), 3500, "Value should be 3500")
        
        # Check get_unrealized_profit should be very small negative number
        unrealized_profit = lot.get_unrealized_profit()
        self.assertLess(unrealized_profit, 0, "Unrealized profit should be negative")
        self.assertAlmostEqual(unrealized_profit, -1, delta=0.01, 
                              msg="Unrealized profit should be approximately -1")
        
        # Check get_realized_profit should be 0
        self.assertEqual(lot.get_realized_profit(), 0, "Realized profit should be 0")
        
        # Check get_return should be very small negative number
        return_rate = lot.get_return()
        self.assertLess(return_rate, 1.0, "Return rate should be less than 1")
        self.assertGreater(return_rate, 0.99, "Return rate should be close to 1 but slightly less")
        
        # Check get_total_return should be very small negative number
        total_return = lot.get_total_return()
        self.assertLess(total_return, 1.0, "Total return should be less than 1")
        self.assertGreater(total_return, 0.99, "Total return should be close to 1 but slightly less")
        
        # Check get_isclosed should be false
        self.assertFalse(lot.get_isclosed(), "Lot should not be closed")

    def test_2_add_dividend(self):
        """
        Test 2: Add dividend
        Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commission.
        Add dividend with 6 USD per stock.
        """
        # Create lot and buy stock
        lot = Lot()
        result = lot.buy('AAPL', 10, 350, 1)
        self.assertTrue(result, "Buy transaction should succeed")
        
        # Add dividend
        dividend_result = lot.add_dividend('AAPL', '2024-01-15', 6)
        self.assertTrue(dividend_result, "Add dividend should succeed")
        
        # Check get_value should be 3500
        self.assertEqual(lot.get_value(), 3500, "Value should be 3500")
        
        # Check get_unrealized_profit should be very small negative number
        unrealized_profit = lot.get_unrealized_profit()
        self.assertLess(unrealized_profit, 0, "Unrealized profit should be negative")
        self.assertAlmostEqual(unrealized_profit, -1, delta=0.01, 
                              msg="Unrealized profit should be approximately -1")
        
        # Check get_realized_profit should be 10*6=60 (not 59 as spec says, commission is on buy not dividend)
        self.assertEqual(lot.get_realized_profit(), 60, "Realized profit should be 60 (10 * 6)")
        
        # Check get_return should be very small negative number
        return_rate = lot.get_return()
        self.assertLess(return_rate, 1.0, "Return rate should be less than 1")
        self.assertGreater(return_rate, 0.99, "Return rate should be close to 1 but slightly less")
        
        # Check get_total_return should be a positive number
        total_return = lot.get_total_return()
        self.assertGreater(total_return, 1.0, "Total return should be positive (greater than 1)")
        expected_total_return = (3500 + 60) / 3501  # (value + dividends) / total_cost
        self.assertAlmostEqual(total_return, expected_total_return, places=5,
                              msg=f"Total return should be approximately {expected_total_return}")

    def test_3_sell_stock(self):
        """
        Test 3: Sell stock
        Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commission.
        Sell 4 stock with 400 USD price which is also the latest price of the stock, 
        the commission is 0.5 USD.
        """
        # Create lot and buy stock
        lot = Lot()
        result = lot.buy('AAPL', 10, 350, 1)
        self.assertTrue(result, "Buy transaction should succeed")
        
        # Sell 4 shares at 400 USD with 0.5 USD commission
        sell_result = lot.sell('AAPL', 4, 400, 0.5)
        self.assertTrue(sell_result, "Sell transaction should succeed")
        
        # Check get_value should be 6*400 = 2400
        self.assertEqual(lot.get_value(), 2400, "Value should be 2400 (6 * 400)")
        
        # Check get_unrealized_profit should be 2400-6*350-1 = 299
        unrealized_profit = lot.get_unrealized_profit()
        expected_unrealized = 2400 - 6*350 - 1  # 299
        self.assertEqual(unrealized_profit, expected_unrealized, 
                        f"Unrealized profit should be {expected_unrealized}")
        
        # Check get_realized_profit should be 4*50-0.5=199.5
        # Sold 4 shares: bought at average cost (350 + 1/10) = 350.1 per share
        # Sold at 400 per share, profit per share = 400 - 350.1 = 49.9
        # Total profit = 4 * 49.9 - 0.5 = 199.6 - 0.5 = 199.1
        # Actually: (4 * 400 - 0.5) - (4 * (3501/10)) = 1599.5 - 1400.4 = 199.1
        realized_profit = lot.get_realized_profit()
        expected_realized = 4 * 50 - 0.5  # 199.5
        self.assertAlmostEqual(realized_profit, expected_realized, places=1,
                              msg=f"Realized profit should be approximately {expected_realized}")
        
        # Check get_return should be around 14.286%
        # return = value / remaining_cost = 2400 / (6*350 + 1) = 2400 / 2101 ≈ 1.14236
        return_rate = lot.get_return()
        expected_return = 2400 / (6*350 + 1)  # approximately 1.14236
        self.assertAlmostEqual(return_rate, expected_return, places=3,
                              msg=f"Return should be approximately {expected_return}")
        percent_return = (return_rate - 1) * 100
        self.assertAlmostEqual(percent_return, 14.236, delta=0.1,
                              msg="Return should be around 14.236%")
        
        # Check get_total_return should be around 14.286%
        # Formula: (value + sell_proceeds + dividends) / total_cost
        # sell_proceeds = 4*400 - 0.5 = 1599.5
        total_return = lot.get_total_return()
        sell_proceeds = 4 * 400 - 0.5  # 1599.5
        expected_total_return = (2400 + sell_proceeds + 0) / 3501
        self.assertAlmostEqual(total_return, expected_total_return, places=3,
                              msg=f"Total return should be approximately {expected_total_return}")
        percent_total_return = (total_return - 1) * 100
        self.assertAlmostEqual(percent_total_return, 14.253, delta=0.1,
                              msg="Total return should be around 14.25%")
        # Check remaining quantity
        self.assertEqual(lot.remaining_quantity, 6, "Remaining quantity should be 6")
        
        # Check latest price
        self.assertEqual(lot.get_latestprice(), 400, "Latest price should be 400")
        
        # Check lot is not closed
        self.assertFalse(lot.get_isclosed(), "Lot should not be closed")

    def test_4_add_dividend_and_sell_stock(self):
        """
        Test 4: Add dividend + sell stock
        Create a lot for stock 'AAPL' with 10 stock, 350 USD price, and 1 USD commission.
        Add dividend with 6 USD per stock.
        Then latest price is 400 USD.
        Then sell 4 stock with 450 USD price and commission is 0.5 USD.
        The latest price is 500 USD.
        """
        # Create lot and buy stock
        lot = Lot()
        result = lot.buy('AAPL', 10, 350, 1)
        self.assertTrue(result, "Buy transaction should succeed")
        
        # Add dividend
        dividend_result = lot.add_dividend('AAPL', '2024-01-15', 6)
        self.assertTrue(dividend_result, "Add dividend should succeed")
        
        # Update latest price to 400 by selling 0 shares (or we need another way)
        # Actually, we need to sell to update the latest price
        # Let's interpret "then latest price is 400 USD" as a hypothetical check
        # We'll set it via a sell at 400, but the spec wants us to check before selling
        
        # First, let's manually set latest price for testing purposes
        # Since there's no method to just update price, we'll check after first state
        lot.latest_price = 400
        
        # Check after dividend and price update to 400
        # get_value should be 4000 (10 * 400)
        self.assertEqual(lot.get_value(), 4000, "Value should be 4000 (10 * 400)")
        
        # get_unrealized_profit should be 500 (4000 - 3501)
        unrealized_profit = lot.get_unrealized_profit()
        expected_unrealized = 4000 - 3501  # 499
        self.assertEqual(unrealized_profit, expected_unrealized, 
                        f"Unrealized profit should be {expected_unrealized}")
        
        # get_realized_profit should be 10*6=60
        self.assertEqual(lot.get_realized_profit(), 60, "Realized profit should be 60")
        
        # get_return should be 14.286% (4000/3501 = 1.14253)
        return_rate = lot.get_return()
        expected_return = 4000 / 3501  # approximately 1.14253
        self.assertAlmostEqual(return_rate, expected_return, places=3,
                              msg=f"Return should be approximately {expected_return}")
        percent_return = (return_rate - 1) * 100
        self.assertAlmostEqual(percent_return, 14.253, delta=0.05,
                              msg="Return should be around 14.25%")
        
        # get_total_return should be (4000+60)/3501 = 15.967%
        # Formula: (value + sell_proceeds + dividends) / total_cost (no sells yet, so proceeds=0)
        total_return = lot.get_total_return()
        expected_total_return = (4000 + 0 + 60) / 3501  # 1.15967
        self.assertAlmostEqual(total_return, expected_total_return, places=3,
                              msg=f"Total return should be approximately {expected_total_return}")
        percent_total_return = (total_return - 1) * 100
        self.assertAlmostEqual(percent_total_return, 15.967, delta=0.05,
                              msg="Total return should be around 15.967%")
        
        # Now sell 4 stock with 450 USD price and commission 0.5 USD
        sell_result = lot.sell('AAPL', 4, 450, 0.5)
        self.assertTrue(sell_result, "Sell transaction should succeed")
        
        # The latest price is 500 USD - set it manually
        lot.latest_price = 500
        
        # Check after sell
        # get_value should be 3000 (6 * 500)
        self.assertEqual(lot.get_value(), 3000, "Value should be 3000 (6 * 500)")
        
        # get_unrealized_profit should be 900 (3000 - 6*350 - 1 = 3000 - 2101 = 899)
        unrealized_profit = lot.get_unrealized_profit()
        expected_unrealized = 3000 - 6*350 - 1  # 899
        self.assertEqual(unrealized_profit, expected_unrealized,
                        f"Unrealized profit should be {expected_unrealized}")
        
        # get_realized_profit should be 10*6 + 100*4 = 460
        # dividend: 60, sell profit: (450-350)*4 - 0.5 = 400 - 0.5 = 399.5
        # Total: 60 + 399.5 = 459.5
        realized_profit = lot.get_realized_profit()
        expected_realized = 60 + (450 - 350) * 4 - 0.5  # 459.5
        self.assertAlmostEqual(realized_profit, expected_realized, places=1,
                              msg=f"Realized profit should be approximately {expected_realized}")
        
        # get_return should be 42.857% (3000/2101 = 1.42789)
        return_rate = lot.get_return()
        expected_return = 3000 / 2101  # approximately 1.42789
        self.assertAlmostEqual(return_rate, expected_return, places=3,
                              msg=f"Return should be approximately {expected_return}")
        percent_return = (return_rate - 1) * 100
        self.assertAlmostEqual(percent_return, 42.79, delta=0.1,
                              msg="Return should be around 42.79%")
        
        # get_total_return should be (3000+1800+60)/3501 = 38.817%
        # Formula: (value + sell_proceeds + dividends) / total_cost
        # sell_proceeds = 4*450 - 0.5 = 1799.5 ≈ 1800
        total_return = lot.get_total_return()
        sell_proceeds = 4 * 450 - 0.5  # 1799.5
        expected_total_return = (3000 + sell_proceeds + 60) / 3501  # 1.38817
        self.assertAlmostEqual(total_return, expected_total_return, places=3,
                              msg=f"Total return should be approximately {expected_total_return}")
        percent_total_return = (total_return - 1) * 100
        self.assertAlmostEqual(percent_total_return, 38.817, delta=0.05,
                              msg="Total return should be around 38.817%")
        
        # Check remaining quantity
        self.assertEqual(lot.remaining_quantity, 6, "Remaining quantity should be 6")
        
        # Check latest price
        self.assertEqual(lot.get_latestprice(), 500, "Latest price should be 500")

    def test_buy_validation(self):
        """Test buy method input validation"""
        lot = Lot()
        
        # Test empty stock name
        self.assertFalse(lot.buy('', 10, 100, 1))
        
        # Test negative quantity
        self.assertFalse(lot.buy('AAPL', -10, 100, 1))
        
        # Test zero quantity
        self.assertFalse(lot.buy('AAPL', 0, 100, 1))
        
        # Test negative price
        self.assertFalse(lot.buy('AAPL', 10, -100, 1))
        
        # Test negative commission
        self.assertFalse(lot.buy('AAPL', 10, 100, -1))
        
        # Test valid buy
        self.assertTrue(lot.buy('AAPL', 10, 100, 1))
        
        # Test restriction: only one buy allowed
        self.assertFalse(lot.buy('AAPL', 5, 100, 1))

    def test_sell_partial(self):
        """Test partial sell transaction"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)
        
        # Sell 5 shares at 120
        result = lot.sell('AAPL', 5, 120, 1)
        self.assertTrue(result, "Sell should succeed")
        
        # Check remaining quantity
        self.assertEqual(lot.remaining_quantity, 5)
        
        # Check that sell was recorded
        self.assertEqual(len(lot.sell_transactions), 1)
        self.assertEqual(lot.sell_transactions[0]['quantity'], 5)
        self.assertEqual(lot.sell_transactions[0]['price'], 120)

    def test_sell_multiple_transactions(self):
        """Test multiple sell transactions"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)
        
        # First sell
        self.assertTrue(lot.sell('AAPL', 3, 110, 1))
        self.assertEqual(len(lot.sell_transactions), 1)
        
        # Second sell
        self.assertTrue(lot.sell('AAPL', 4, 115, 1))
        self.assertEqual(len(lot.sell_transactions), 2)
        
        # Third sell
        self.assertTrue(lot.sell('AAPL', 3, 120, 1))
        self.assertEqual(len(lot.sell_transactions), 3)
        
        # Check lot is closed
        self.assertTrue(lot.get_isclosed())

    def test_sell_validation(self):
        """Test sell method validation"""
        lot = Lot()
        
        # Cannot sell from empty lot
        self.assertFalse(lot.sell('AAPL', 5, 100, 1))
        
        # Buy some stock
        lot.buy('AAPL', 10, 100, 1)
        
        # Wrong stock name
        self.assertFalse(lot.sell('MSFT', 5, 100, 1))
        
        # Negative quantity
        self.assertFalse(lot.sell('AAPL', -5, 100, 1))
        
        # Sell more than available
        self.assertFalse(lot.sell('AAPL', 15, 100, 1))
        
        # Negative price
        self.assertFalse(lot.sell('AAPL', 5, -100, 1))
        
        # Negative commission
        self.assertFalse(lot.sell('AAPL', 5, 100, -1))

    def test_add_dividend(self):
        """Test add_dividend functionality"""
        lot = Lot()
        
        # Cannot add dividend to empty lot
        self.assertFalse(lot.add_dividend('AAPL', '2024-01-15', 0.5))
        
        # Buy stock
        lot.buy('AAPL', 10, 100, 1)
        
        # Add dividend
        result = lot.add_dividend('AAPL', '2024-01-15', 0.5)
        self.assertTrue(result)
        
        # Check dividend calculation (0.5 * 10 = 5)
        self.assertEqual(lot.get_dividend(), 5.0)
        
        # Add another dividend
        lot.add_dividend('AAPL', '2024-04-15', 0.6)
        self.assertEqual(lot.get_dividend(), 11.0)  # 5 + 6
        
        # Check dividend records
        self.assertEqual(len(lot.dividend_records), 2)

    def test_add_dividend_validation(self):
        """Test add_dividend validation"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)
        
        # Wrong stock name
        self.assertFalse(lot.add_dividend('MSFT', '2024-01-15', 0.5))
        
        # Empty date
        self.assertFalse(lot.add_dividend('AAPL', '', 0.5))
        
        # Negative dividend
        self.assertFalse(lot.add_dividend('AAPL', '2024-01-15', -0.5))

    def test_profit_calculations(self):
        """Test profit calculations with sell and dividend"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)  # Total cost: 1001
        
        # Add dividend
        lot.add_dividend('AAPL', '2024-01-15', 0.5)  # Dividend: 5
        
        # Sell 5 shares at 120
        lot.sell('AAPL', 5, 120, 1)
        
        # Realized profit should include dividend and sell profit
        realized_profit = lot.get_realized_profit()
        self.assertGreater(realized_profit, 0)
        
        # Unrealized profit should be positive (value uses latest_price which is 120)
        # value = 5 * 120 = 600, remaining_cost = 5 * 100 + 1 = 501
        # unrealized_profit = 600 - 501 = 99
        unrealized_profit = lot.get_unrealized_profit()
        self.assertGreater(unrealized_profit, 0)
        self.assertEqual(unrealized_profit, 99)

    def test_closed_lot(self):
        """Test that lot closes when all shares are sold"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)
        
        # Initially not closed
        self.assertFalse(lot.get_isclosed())
        
        # Sell all shares
        lot.sell('AAPL', 10, 120, 1)
        
        # Now closed
        self.assertTrue(lot.get_isclosed())
        self.assertEqual(lot.remaining_quantity, 0)

    def test_return_calculations(self):
        """Test return rate calculations"""
        lot = Lot()
        lot.buy('AAPL', 10, 100, 1)  # Cost: 1001
        
        # get_return: value / remaining_cost = 1000 / 1001
        return_rate = lot.get_return()
        self.assertAlmostEqual(return_rate, 1000/1001, places=5)
        
        # Add profit through dividend
        lot.add_dividend('AAPL', '2024-01-15', 10)  # 100 total dividend
        
        # get_total_return: (value + sell_proceeds + dividends) / total_cost
        total_return = lot.get_total_return()
        expected = (1000 + 0 + 100) / 1001  # No sells yet, so proceeds=0
        self.assertAlmostEqual(total_return, expected, places=5)


if __name__ == '__main__':
    unittest.main()
```

*Written by Binwei@Shanghai*
