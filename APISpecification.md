# Auto-Garcon REST API

Written By Tyler Beverley, Sosa Edison. 
This is a living document that will act as the documentation for the API that will be used by Auto-Garcon. Be sure to check this document often as it will change as the API changes. There are several sections in this document, the first of which details how to send requests to the API. frequently used datastructures and details their inner components. The name of the data structure might be used as short hand further in the document. The next section details the various endpoints that are available. 

Naming of JSON feilds follows cammelCase convention. 

## Connecting

The base URL to send requests to is: [TBD].  
 
The API accepts GET and POST requests. POST parameters are to be in JSON format. GET requests will be used for retreiving data (reading) and POST for creating, updating, and removing data.  

## Data Structures 
Various data structures and enums that will be used commonly in the API. Enums are denoted by curly braces, and optional arguments are surrounded in brackets. Anything followed by an astrerix indicates it is likely to change. Time is writen in 24 hour time (i.e Midnight = 2400). Types are denoted after the variable name and a colon.  
  
MenuType = { Dinner, Breakfast, Brunch, Breakfast, Lunch, Drink }  
OrderStatus = { Open, Closed }  
MenuStatus = { Draft, Active, Deleted \* }  
Category = Any User Defined String  
Allergens = { Meat, Diary, Nuts, Gluten, Nuts, Soy, Other } `
  
* _MenuItem_
  * category : Category
  * name : text 
  * description : text 
  * [image] : bytes \* 
  * allergens : text 
  * [spice] : text \* 
  * price : float
  * allergens:  Allergen 

* _Menu_  
  * menuID : int
  * status: MenuStatus
  * type : MenuType 
  * timeRange[] 
    * start : int 
    * stop : int 
  * numItems : int 
  * items[] : MenuItems 

* _Order_
  * orderID : int 
  * resturantID : int 
  * numMenuItems : int 
  * items[] : MenuItem[]
  * chargeAmmount : float
  * customerName : string
  * [customizations] : text
  * status : OrderStatus

## Endpoints 

This section details the various endpoints available on the API. All characters in the URL will be lowercase. A part of a route that is lead by a colon indicates a parameter in the URL. So /users/:userid will look like /users/12314. Feilds with no data type specifed are inferred. All endpoints will respond with a HTTP status code and any specifed JSON response. 
  
The documentation follows this style: 

* parent route
 * HTTP Method: full route  
   * query paramaters in the format of: ?param=[options]
   * Request JSON paramaters for POST requests. 
   * Response JSON
  
---  


* /users
   * GET /users/:userid 
    * Response: 
      * userID
      * firstName
      * lastName
   * GET /users/:userid/favorites
     * ?resturantid=int 
     * Response: 
       * numResturants
       * resturants[] 
         * resturantID
         * resturantName
         * favorites: Items[] 
   * GET /users/:userid/orders
      * ?range=int
      * Response: Order Structure
   * POST /users/newuser 
     * Request: 
       * firstName
       * lastName
       * email
     * Response: 
       *  userID
* /restaurant   
  * GET /restaurant/:restaurantid
    * Response:  
      * resturantID
      * resturantName
      * resturantAddress
      * availableMenuTypes: MenuType[]
  * GET /restaurant/random
    * Response: 
      * restaurantID
      * restaurantName
      * restaurantAddress
      * availableMenuTypes: MenuType[]
  * GET /restaurant/:restaurantid/orders
    * ?status=[open, closed, all]
    * ?userid=int
    * Response:
      * Orders: OrderStructure[]
  * GET /restaurant/:restaurantid/menu
  	* ?type=MenuType
    * Response: 
       * menu : Menu
  * GET /restaurant/:restaurantid/tables
    * Response: 
      * numTables
      * tableIDs: int[]
  * POST /restaurant/:restaurantid/table/:tableID/sitdown
    * Response: 
      * restaurantID
      * restaurantName
      * tableNumber
      * userID
  * POST /restaurant/:restaurantid/menu/add
    * Request: 
      * menu : Menu
    * Reponse: 
      * menuID
  * POST /restaurant/:restaurantid/menu/remove
     * Request: menuID
  * POST /restaurant/:restaurantid/orders/sumbit
    * Request:
      * customerID
      * chargeAmount
      * tableID
      * items : MenuItems[] 
      * customizations : text
    * Response: 
      * orderID 
  * POST /restaurant/:restaurantid/orders/complete
     * orderID


Notes 
--------
possible upcoming change to how menuItem's images are uploaded to the API.
