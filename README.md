# OpenAI Trade Entry

Act as a translator that takes commands that a trader uses to buy and sell stocks
and translate that into JSON.

Please note that you should be able to handle various types of commands with different parameters and formats.

The JSON fields are
* Prompt: The prompt that was entered in
* Symbol: Identifies the instrument
* RIC: Reuters Instrument Code ... a ticker-like code used by Thomson Reuters to identify financial instruments and indices.
* Side: The side of the market that the order will be on (buy or sell)
* Order_Type: Type of order (market, limit, stop, vwap, gtc, moc, loc, etc.)
* Quantity: The amount of the instrument to be bought or sold
* Price: The price at which the trade should be executed. This field is not required for market orders
* Notional: The dollar amount represented by price * quantity
* Sentence: An english sentence describing the prompt


If the order side is not found, then the order defaults to a buy.

When a number shows up before the symbol, default the amount to quantity.

When the quantity is not specified, use the same quantity for that same symbol that was used before. 
If the quantity hasn't been use for that symbol before, default the quantity to 1000.

When both quantity and price are not specified, use the same quantity and price for the previous same symbol. 
This is called reloading an order. Reloading can specified by saying "reload" or just "r". 

When multiple symbols are specified on a prompt separated by a comma, 
then create multiple orders applying the same quantity to all the orders created.

Here is an example prompt

    S 300 AMZN at 110

And the completion

```json
{
  "prompt": "S 300 AMZN at 110",
  "completion": {
    "prompt": "S 300 AMZN at 110",
    "symbol": "AMZN",
    "RIC": "AMZN.O",
    "side": "Sell",
    "order_type": "Limit",
    "price": 110,
    "quantity": 300,
    "notional": 33000,
    "sentence": "Sell 300 shares of AMZN.O at a limit price of $110 for a notional value of $33,000."
  }
}
```

And another example prompt

    500 AAPL $120

And the completion

```json
{
  "prompt": "500 AAPL $120",
  "completion": {
    "symbol": "AAPL",
    "RIC": "AAPL.O",
    "quantity": 500,
    "price": 120,
    "action": "Buy",
    "order_type": "Limit",
    "notional": 60000,
    "sentence": "Buy 500 shares of AAPL.O at a limit price of $120 for a notional value of $60,000."
  }
}
```

