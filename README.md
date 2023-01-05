# Project 5: Webcrawler

## High-Level Approach

Our webcrawler implementation succeeds in crawling Fakebook and finding all secret flags by splitting up functionality across dedicated classes. There are two main classes that work in tandem. Our ParsePage class, which is capable of parsing any HTML page the crawler encounters with the help of the HTMLParser library. On the other hand, our Crawler class handles all other functionality including establishing socket connections, sending/receiving HTML requests, and crawling the many pages on Fakebook.

The crawler starts off by sending a GET request to Fakebook's login page /accounts/login to obtain the necessary cookies. To actually log in, the crawler sends a POST request with the CSRF token, username/password, and all other request headers needed in HTTP 1.1. After loging in, the program obtains the session cookie from the response that is used in each subsequent request to crawl the rest of Fakebook. The crawl begins by sending a valid GET request to the root of Fakebook (/fakebook/) and parses the page to find any sub-pages (elements with href tag), adds this to the Frontier of unvisited pages, and crawls all pages on the Frontier. This process is repeated for all pages that we have not yet visited, which our program is able to check because after crawling a page once we add this to an array. The Parser class breaks each page down and checks for flags. The crawler runs until all (5) flags are found.

## Challenges Faced

Figuring out how to manage connections for correctness and efficiency was a signifcant challenge. Initially, we started with "Connection: close" that opened a socket for each request, but was way too slow. After this, we tried changing to "Connection: keep-alive" so that our program did not need to open a new socket for each GET request. Using one socket for the entire process resulted in various HTTP errors and unsuccesful crawls. Eventually, we ended up solving our problem by creating a new socket every 100 requests, but this was not intuitive from the start.

Creating valid POST and GET requests that adhered to HTTP 1.1 and were able to succesfully do what we wanted them to took some time as well. These requests needed to adhere to very specific formats that took some trial and error to figure out. The resources provided were helpful, but did not give a complete picture of what exactly we needed to communicate with Fakebook over HTTP 1.1.

Furthermore, being able to process HTML string responses was crucial to obtaining flags, cookies, links, etc. , but we had little experience with Python's HTMLParser library. For the CSRF cookie specifically, it was unclear wether this was obtained from the cookies in the HTTP header or HTML form/contents. There were cookies present throughout responses, but not all of them led to successful POST/GET requests. Figuring out which cookies need to be used for what and where was definitely a challenge.

## Testing

The main testing and debugging strategy we used was a plethora of print statements that gave us the ability to "peak under the hood" of our crawler's progress and communication with Fakebook. With these print statements, we were able to test if our program outputs what we want based on our request contents + format, socket usage, and code overall. For example, if we wanted to test a log-in GET request, we could print the output of what we send to see if it is appropriate. We found this testing approach to be quite useful.

A specific aspect of our programs that required testing was different socket settings with timeout and different time values to keep connections alive for. These aspects did not adhere to default values and finding the right value or settings required significant testing of different ones. Transitioning from HTTP 1.0 to 1.1 also required testing to see if and where our program broke based on the switch -- then fixing what we needed as necessary.
