# GCO Vulnerabilities 
No new high or median alerts showed up on any of the GCO pages. 
However, the GCO Inspection Report page prompted a Buffer Overflow alert. 
This median alert was concluded to be highly unlikely due to the fact that the language used is Java. Buffer Overflows are more prevalent in languages such as C/C++.
These languages are not designed to have inherent bounds checking unlike Java.
Even though Java may prevent a buffer overflow from becoming a security issue, it is essential for all programmers to understand it and handle it in the programs securely.

## [M] Buffer Overflow 
- Occurs when the amount of data in the buffer exceeds its storage capacity
- The extra data overflows into adjacent memory locations 
  - This corrupts or overwrites the data 
- This vulnerability will typically appear when code is:
1. reliant on external data to control its behavior 
2. dependent on external properties that are enforced beyond its scope
3. so complex that programmers are not able to predict its outcome accurately
#### Preventions:
- Validate user input data
  - Write the program using proper return length checking
- Use buffer-size accessors when possible
- Ensure integer variables are within proper bounds before using it as an index to an array
- Use alternative data structures that reduce the risk of overflows
  - Use vectors and iterators instead of arrays when possible
  - Vectors are dynamically allocated, whereas arrays have a fixed size structure. This wont eliminate the problem but it may greatly reduce the risk
 
