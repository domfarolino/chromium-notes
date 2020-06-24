# cs searching

Express file extensions:

 - Search for an expression something with a given
     - `referrer file:mojom$`
 - Search for an expression that can be split by something
     - `const\ char.*\"chrome:// file:cc$`
     - In this case we're searching for `const char {???} "chrome://`
