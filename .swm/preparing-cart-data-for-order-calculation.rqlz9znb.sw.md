---
title: Preparing Cart Data for Order Calculation
---
This document describes how shopping cart data is prepared for order calculation during checkout. The flow validates promo codes, removes expired codes, and filters out unavailable products, resulting in an order summary ready for final calculation.

# Where is this flow used?

This flow is used multiple times in the codebase as represented in the following diagram:

```mermaid
graph TD;
      74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.calculateShoppingCartTotal) --> ddc887b299f7e9c44d344d88b2b0bbfd4f2afb5cc9e3eb61b8bb2a7e4659088e(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.caculateShoppingCart)

6334a5a0cbcdd387505220d4a219068cfbb930a30fd6c8889f3254492bb78e98(sm-core/…/shoppingcart/ShoppingCartCalculationServiceImpl.java::ShoppingCartCalculationServiceImpl.calculate) --> 74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.calculateShoppingCartTotal)

74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.calculateShoppingCartTotal) --> ddc887b299f7e9c44d344d88b2b0bbfd4f2afb5cc9e3eb61b8bb2a7e4659088e(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.caculateShoppingCart)

6334a5a0cbcdd387505220d4a219068cfbb930a30fd6c8889f3254492bb78e98(sm-core/…/shoppingcart/ShoppingCartCalculationServiceImpl.java::ShoppingCartCalculationServiceImpl.calculate) --> 74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(sm-core/…/order/OrderServiceImpl.java::OrderServiceImpl.calculateShoppingCartTotal)


classDef mainFlowStyle color:#000000,fill:#7CB9F4
classDef rootsStyle color:#000000,fill:#00FFF4
classDef Style1 color:#000000,fill:#00FFAA
classDef Style2 color:#000000,fill:#FFFF00
classDef Style3 color:#000000,fill:#AA7CB9

%% Swimm:
%% graph TD;
%%       74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.calculateShoppingCartTotal) --> ddc887b299f7e9c44d344d88b2b0bbfd4f2afb5cc9e3eb61b8bb2a7e4659088e(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.caculateShoppingCart)
%% 
%% 6334a5a0cbcdd387505220d4a219068cfbb930a30fd6c8889f3254492bb78e98(<SwmPath>[sm-core/…/shoppingcart/ShoppingCartCalculationServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/shoppingcart/ShoppingCartCalculationServiceImpl.java)</SwmPath>::ShoppingCartCalculationServiceImpl.calculate) --> 74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.calculateShoppingCartTotal)
%% 
%% 74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.calculateShoppingCartTotal) --> ddc887b299f7e9c44d344d88b2b0bbfd4f2afb5cc9e3eb61b8bb2a7e4659088e(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.caculateShoppingCart)
%% 
%% 6334a5a0cbcdd387505220d4a219068cfbb930a30fd6c8889f3254492bb78e98(<SwmPath>[sm-core/…/shoppingcart/ShoppingCartCalculationServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/shoppingcart/ShoppingCartCalculationServiceImpl.java)</SwmPath>::ShoppingCartCalculationServiceImpl.calculate) --> 74fb03dda3973b3863f4157261362a51c00c9784c1e747b4b0b168972aa16907(<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>::OrderServiceImpl.calculateShoppingCartTotal)
%% 
%% 
%% classDef mainFlowStyle color:#000000,fill:#7CB9F4
%% classDef rootsStyle color:#000000,fill:#00FFF4
%% classDef Style1 color:#000000,fill:#00FFAA
%% classDef Style2 color:#000000,fill:#FFFF00
%% classDef Style3 color:#000000,fill:#AA7CB9
```

# Preparing Cart Data for Order Calculation

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
  node1["Start cart calculation"]
  click node1 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:430:433"
  node1 --> node2{"Promo code present?"}
  click node2 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:436:436"
  node2 -->|"No"| node5["Filter unavailable products"]
  node2 -->|"Yes"| node3{"Promo code valid (added within 1 day)?"}
  click node3 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:438:446"
  node3 -->|"Yes"| node4["Apply promo code"]
  click node4 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:447:448"
  node3 -->|"No"| node6["Clear promo code and update cart"]
  click node6 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:450:452"
  node4 --> node5
  node6 --> node5
  
  subgraph loop1["Filter unavailable products"]
    node5["Filter out unavailable products from cart"]
    click node5 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:455:457"
  end
  node5 --> node10["Calculate order summary"]
  click node10 openCode "sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java:461:461"
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%   node1["Start cart calculation"]
%%   click node1 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:430:433"
%%   node1 --> node2{"Promo code present?"}
%%   click node2 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:436:436"
%%   node2 -->|"No"| node5["Filter unavailable products"]
%%   node2 -->|"Yes"| node3{"Promo code valid (added within 1 day)?"}
%%   click node3 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:438:446"
%%   node3 -->|"Yes"| node4["Apply promo code"]
%%   click node4 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:447:448"
%%   node3 -->|"No"| node6["Clear promo code and update cart"]
%%   click node6 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:450:452"
%%   node4 --> node5
%%   node6 --> node5
%%   
%%   subgraph loop1["Filter unavailable products"]
%%     node5["Filter out unavailable products from cart"]
%%     click node5 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:455:457"
%%   end
%%   node5 --> node10["Calculate order summary"]
%%   click node10 openCode "<SwmPath>[sm-core/…/order/OrderServiceImpl.java](sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java)</SwmPath>:461:461"
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

This section prepares the shopping cart data for order calculation by validating promo codes, removing expired codes, filtering out unavailable products, and assembling a cleaned order summary.

| Category        | Rule Name                     | Description                                                                                                                               |
| --------------- | ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Data validation | Promo code validity window    | If a promo code is present in the shopping cart, it must have been added within the last 1 day to be considered valid for the order.      |
| Data validation | Unavailable product exclusion | Only products marked as available are included in the order summary; unavailable products are filtered out and excluded from calculation. |
| Business logic  | Expired promo code removal    | If the promo code is expired (added more than 1 day ago), it must be cleared from the cart and not applied to the order.                  |
| Business logic  | Order summary preparation     | The cleaned order summary, containing only available products and a valid promo code, is passed to the order calculation process.         |

<SwmSnippet path="/sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java" line="430">

---

CaculateShoppingCart validates the promo code's age, removes expired codes, filters out unavailable products, and hands off the cleaned order summary to <SwmToken path="sm-core/src/main/java/com/salesmanager/core/business/services/order/OrderServiceImpl.java" pos="461:3:3" line-data="    	return caculateOrder(orderSummary, customer, store, language);">`caculateOrder`</SwmToken> for the final calculation.

```java
    private OrderTotalSummary caculateShoppingCart( ShoppingCart shoppingCart, final Customer customer, final MerchantStore store, final Language language) throws Exception {


    	OrderSummary orderSummary = new OrderSummary();
    	orderSummary.setOrderSummaryType(OrderSummaryType.SHOPPINGCART);

    	if(!StringUtils.isBlank(shoppingCart.getPromoCode())) {
    		Date promoDateAdded = shoppingCart.getPromoAdded();//promo valid 1 day
    		if(promoDateAdded == null) {
    			promoDateAdded = new Date();
    		}
    		Instant instant = promoDateAdded.toInstant();
    		ZonedDateTime zdt = instant.atZone(ZoneId.systemDefault());
    		LocalDate date = zdt.toLocalDate();
    		//date added < date + 1 day
    		LocalDate tomorrow = LocalDate.now().plusDays(1);
    		if(date.isBefore(tomorrow)) {
    			orderSummary.setPromoCode(shoppingCart.getPromoCode());
    		} else {
    			//clear promo
    			shoppingCart.setPromoCode(null);
    			shoppingCartService.saveOrUpdate(shoppingCart);
    		}
    	}

    	List<ShoppingCartItem> itemList = new ArrayList<ShoppingCartItem>(shoppingCart.getLineItems());
    	//filter out unavailable
    	itemList = itemList.stream().filter(p -> p.getProduct().isAvailable()).collect(Collectors.toList());
    	orderSummary.setProducts(itemList);


    	return caculateOrder(orderSummary, customer, store, language);

    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc2hvcGl6ZXIlM0ElM0FTd2ltbS1EZW1v" repo-name="shopizer"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
