### Label
In Kubernetes, labels are a fundamental concept used to organize and select subsets of objects, such as pods, services, and deployments. 
They are key/value pairs attached to objects and can be used in various ways, from specifying scheduling policies to enabling service discovery.

#### Label Structure
A label consists of a key and a value, both of which are strings. 
The key is divided into two parts: an optional prefix and a mandatory name, separated by a slash (/). 
This structure allows for namespacing and greater control over label keys.

- Name Segment
    - Mandatory part of the key.
    - Must be 63 characters or less.
    - Should begin and end with an alphanumeric character ([a-z0-9A-Z]).
    - Can include dashes (-), underscores (_), dots (.), and alphanumerics between.
- Prefix Segment
    - Optional part of the key.
    - Used for namespacing purposes.
    - Must be a DNS subdomain.
    - A DNS subdomain is composed of a series of DNS labels separated by dots (.).
    - Each label must start and end with an alphanumeric character with dashes allowed in between.
    - The entire prefix must not exceed 253 characters in length. Followed by a slash (/), if specified.
  
#### Let's try to understand the use case for Prefixes
Consider a scenario where multiple teams are working on a project and share a Kubernetes cluster. 
Implementing namespacing with prefixes helps prevent collisions between label keys and clarifies the scope or ownership of a label.

For instance, different teams might utilize their team domain as a prefix to distinguish their labels, such as team1.example.com/role/webapp versus team2.example.com/role/webapp. 
In the provided example, team1.example.com/role is the key, and webapp is the value, where team1.example.com is the prefix and role is the name segment.
