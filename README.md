# IBV-Verbs-API-Shared-Key-Value-Store

**To be run on Infiniband compatible hardware**

## Instructions:
To run the shared Key-Value store, one device will be assigned the role of Server, and the other will be the Clients.

In a standard use case, the Server will be ran by starting the Server binary.
A Client will be ran by starting the Client binary, with an additional input of the Server's IP, in the following way:
./Client Server-IP

Notice that N_CLIENTS in the bw_template header file pre-defines the number of Clients that will be connected to the Server.
For the Key-Value code to begin, N_CLIENTS number of Clients must connect to the Server, and then the relevant code will start.

There are 3 avaliable modes of operation to access the Key-Value store on the Server for a Client:
1. throughput - will perform a test of sending many SET and GET requests between the Server and Client and will print out the gotten throughput of the system
2. file - given a name of a txt file in-which each line represents a command that the Client activates, line-by-line. The avaliable commands will be laid out in the next section.
3. text - creates a simple terminal text input wherein the Client's user can write one of the given commands(laid out in the next section).

To access one of these modes, after starting up a Client and connecting it to the Server, you must write the name of the mode.
Different Clients may use different modes at the same time.

## Avaliable commands:
The commands that a client can send out are as follows:
1. set key value - sends the key and the value to be saved in the Server as given.
2. setsize key value_size - sends the key with the size of the value so that the Server creates a location in memory of the given value size.
    By default, the value that is created is random(but this is for demonstation purposes, as it can be set to a non-random value as desired).
    Also by default, according to the MAX_KEY_SIZE in the bw_template header file, if the value and key are together larger than 4096 bytes
    the value will be transferred over to the Server with an IBV RDMA Write request to save on a memory copy for large stacks of data.
3. get key - receives the value for the given key from the Server and saves it in the local Key-Value store of the Client.
    In the event that the key and value pair is larger than MAX_KEY_SIZE as described above, we transfer the value with an IBV RDMA Read request.
4. printkv - prints out the Key-Value stores of the Client and Server.
5. warmup - sends a small parcel of data without prints to create a warmup effect for the hardware.

## Additional information:
PRINT_MSGS in the bw_template.c file can be set to either boolean value to print all the exact messages that are sent between the Server and Clients in the operation of the system.
If it left as false, you will still be able to print the Key-Value store and be shown them.

The Client1.txt and Client2.txt are examples of files that can be used in the "file" mode by a Client.

To compile the code with GCC:
'''
gcc bw_template.c linkedlist_pairs.c -libverbs -o server && ln -s server client 
'''
