# Auto-Garcon REST API

Written By Tyler Beverley, Sosa Edison, and Tyler Reiland. 
This is a living document that will act as the documentation for the API that will be used by Auto-Garcon. Be sure to check this document often as it will change as the API changes. There are several sections in this document, the first of which details how to send requests to the API. frequently used datastructures and details their inner components. The name of the data structure might be used as short hand further in the document. The next section details the various endpoints that are available. 

Naming of JSON fields follows camelCase convention. 

## Connecting

The base URL to send requests to is: https://autogarcon.live/api.  
 
The API accepts GET and POST requests. POST parameters are to be in JSON format. GET requests will be used for retreiving data (reading) and POST for creating, updating, and removing data.  

## Data Structures 
Various data structures and enums that will be used commonly in the API. Enums are denoted by curly braces, and optional arguments are surrounded in brackets. Anything followed by an astrerix indicates it is likely to change. Time is writen in 24 hour time (i.e Midnight = 2400). Types are denoted after the variable name and a colon.  
  
OrderStatus = { OPEN, CLOSED }  
MenuStatus = { DRAFT, ACTIVE }  
Allergens = { MEAT, DAIRY, NUTS, GLUTEN, NUTS, SOY, OTHER }  
Category = Any User Defined Category Name.  
MenuName = User Defined MenuName.  
   
* _MenuItem_
  * itemID : int
  * itemName : string 
  * description : string 
  * category : string
  * price : float
  * allergens: Allergen[]
  * calories: int
  * imagePath: string


* _Menu_  
  * menuID : int
  * timeRanges: []  
    * startTime : int 
    * endTime : int 
  * menuStatus : int
  * menuName : string
  * restaurantID : int
  * menuItems?: MenuItems[]

* _Order_
  * orderID : int 
  * tableID : int
  * customerID: int 
  * orderTime : Timestamp
  * status : OrderStatus 
  * chargeAmount : float 
  * resturantID : int 
  * OrderItem[]
    
 * _OrderItem_
   * orderItemID : int
   * menuItemID : int
   * itemName : string
   * quantity : int
   * comments : string
   * orderID : int;
   * price : float
   
 * _Restaurant_
   * restaurantID : int
   * restaurantName : string
   * description : string
   * address : string
   * city : string
   * zipCode : int
   * country : string
   * state : string
   * primaryColor : string (ex: "#ffffff")
   * secondaryColor : string (ex: "#ffffff")
   * menus : Menu[]
   * numTables : int 
  
## Endpoints 

This section details the various endpoints available on the API. All characters in the URL will be lowercase. A part of a route that is lead by a colon indicates a parameter in the URL. So /users/:userid will look like /users/12314. Feilds with no data type specifed are inferred. All endpoints will respond with a HTTP status code and any specifed JSON response. 
  
The documentation follows this style: 

* parent route
 * HTTP Method: full route  
   * query paramaters in the format of: ?param=[options]
   * Request JSON paramaters for POST requests. 
   * Response JSON
  
---  
All Endpoints listen here are implemented, and if you get a 404 please contact us to let us know!


---

* POST /image/:filename 
   * Used for image upload.  
* GET /images/:filename 
   * get images that have been uploaded. 
* GET /tables
  * ?alexaid=
* /users
   * GET /users/:userid 
    * Response: 
      * userID
      * firstName
      * lastName
   * GET /users/:userid/favorites
     * Response: 
       * Restaurant[]
     * Note: Contains menus but not menu items. Will return all menus for the restaurant regardless of status.
   * POST /users/:userid/favorites/restaurant/:restaurantid/add
      * Response: HTTP Status Code
   * POST /users/:userid/favorites/restaurant/:restaurantid/remove
      * Response: HTTP Status Code
   * GET /users/:userid/orders
      * Response: Order Structure
      * NOTE: Gets all the user's orders within 24 hours
   * POST /users/signin 
     * Request: 
       * firstName
       * lastName
       * email
     * Response: 
       * userID
       * firstName
       * lastName
       * email
       * restaurantID (the restaurant the user manages, -1 means they don't manage any restaurants)
   * POST /users/addmanager
     * Request: 
       * email
       * restaurantID
     * Response: 
       * HTTP status code
* /restaurant
  * GET /restaurant
    * Response:  
      * numRestaurants: int 
      * restaurants: Restaurant[] (All of the possible restaurants)
    * Note: Does not contain menus or menu items
  * GET /restaurant/:restaurantid
    * Response:  
      * Restaurant
    * Note: Does not contain menus or menu items
  * GET /restaurant/:restaurantid/withmenus
    * Response:  
      * Restaurant
    * Note: Contains menus and menu items, but only the ones available at that current moment.
  * GET /restaurant/:restaurantid/order
    * Response:
      * Orders: OrderStructure[] (All orders at that restaurant)
  * GET /restaurant/:restaurantid/menu
  	* ?name=MenuName
    * Response: 
       * menu : Menu
  * GET /restaurant/:restaurantid/menu/available
    * Response: 
       * menu : Menu[] (Only the menus that are available at the current time)
  * GET /restaurant/:restaurantid/tables
    * Response: 
      * numTables
      * tableIDs: int[]
  * GET /restaurant/:restaurantid/tables/create?numtables=x
    * Response: 
      * HTTP status code
    * Note: Replace 'x' in the query parameter with the number of tables you want to add (1-100)
  * POST /restaurant/:restaurantid/menu/add
    * Request: 
      * menu : Menu
    * Reponse: 
      * menuID
  * POST /restaurant/:restaurantid/menu/:menuid/remove
     * Response: HTTP status code
  * POST /restaurant/:restaurantid/menu/:menuid/item/:itemid/remove
     * Response: HTTP status code
     * Note: Removes the menu item from only the specified menuid
  * POST /restaurant/:restaurantid/menu/:menuid/item/:itemid/removefromall
     * Response: HTTP status code
     * Note: Removes the menu item from all menus
  * POST /restaurant/:restaurantid/tables/:tablenumber/register
     * Request: 
       * alexaID: string
  * POST /restaurant/:restaurantid/tables/:tablenumber/order/new
    * Request: 
      * customerID : int
    * Response: 
      * HTTP status code
    * Note: Creates the shell for an order in memory so the Alexa at that table can build on it
  * POST /restaurant/:restaurantid/tables/:tablenumber/order/add
    * Request: OrderItem
        * menuItemID : int
        * menuID : int
        * quantity : int
        * comments : string
     * Response: 
       * HTTP status code
  * POST /restaurant/:restaurantid/tables/:tablenumber/order/remove
    * Request: OrderItem
      * menuItemID : int
  * GET /restaurant/:restaurantid/tables/:tablenumber/order/submit
    * Response: 
      * HTTP status code
  * POST /restaurant/:restaurantid/tables/:tablenumber/order/submitfull
    * Request: Order
      * customerID
      * OrderItem[]
        * menuItemID : int
        * menuID : int
        * quantity : int
        * comments : string
    * Response: 
      * HTTP status code

  * POST /restaurant/:restaurantid/order/:orderid/complete
     * Note: mark an order as completed / ready for pickup. 


