# QR Specification

## Tasks
The Auto-Garcon QR codes must accomplish three main task:
* Identify which restaurant the user is at
* Identify which table the user is at
* Provide a link to download the Auto-Garcon Android application from a normal smartphone QR-scanner

## Format
The QR codes will hold the following format:
* https://autogarcon.live/download?restaurant=restaurantID&table=tableID

## Generation 
A QR-code generation API such as api.qrserver.com may be used by the Auto-Garcon Web Team to dynamically generate 
the needed QR-Codes 

## API Requests / Response
* Once the Android application scans a QR code, the application will parse the url and retrieve the restaurant and table IDs
* The application will then do a series of HTTP requests to the server
1. Ensure that the restaurant ID is a valid restaurant and get the restaurant menus if it is
2. Ensure that the table ID is valid
3. POST the "sitdown" data to the server
