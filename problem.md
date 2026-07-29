

## Example Description of What’s missing from the web

The browser doesn’t store history linked to an “‘id’, it just stores history. When an ‘id’ is connected to the history, multiple histories can emerge, for the history that is tied to its ‘id’. Consider these ‘ids’ to be integers. 

The user remembers them, and can switch between them on the browser. btw, This is equivalent to switching thinking spaces on the browser. 

As each ‘id’ represents a different history, they can be shown separate suggestions linked to that ‘id’. Now let’s take this a step further. 

protocol://username:password@hostname:port/pathname?search#hash
--------------------------href----------------------------
                            ----host------
-----------------origin-------------------


Imagine it to be (yet to be discussed for the right solution)
protocol://username:password:id@hostname:port/pathname?search#hash

When the user switches the ‘id’ from the interface, a new “data sink” is activated on the host. Instead of having a different username, password for each data sink. The ‘id’ works as a multi-value switch.

But that is half the problem, and it can’t be implemented like this. The problem is more complex.
- A ‘id’ for the server, is not the same as an ‘id’ for the user. The user workspace can have multiple server ‘ids’.
- So there are two types of ‘ids’. One for the user, one for the Server.
- **There are other things as well**

