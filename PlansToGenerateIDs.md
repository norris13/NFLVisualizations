
# Map to and from nflFastR player IDs

### My vision is to create a table with the following columns:

- PlayerFirstName
    - simple, like 'Nick'
- PlayerFirstName
    - again, simple. Like "Foles"
- PlayerInitialsName
    - "N.Foles"
- NewPlayerID
    - we can work on a better, more descriptive, column name
    - I would recomend just making it whatever the row number is with enough leading 0s that it is ~8 characters long?
        - by number of rows I mean that the first row is going to be a Cardinals player from '99 because Arizona is first alphabetically. So whoever their QB was in 1999 would have the ID number 00000001 and the next player would be 00000002 and so on
    - my idea for having our own internal ID number is because a player's ID number currently is changing from year to year (I think). So Nick Foles' player ID in 2017 isn't the same as the year before. This makes it harder to keep track of players from year to year, team to team.
        - Another benefit is this new player ID could be used for plenty of other tables I think would be useful, such as player positions, snap count by week, salary information (I have some leads on that which I will start working on once (if) I can get this to work in the first place), etc.
- NflFastRPlayerID
    - this would be the current player ID system that we have currently, but as a list.
        - admittedly I am not sure what the best way to structure this is. My gut tells me something like a python dictionary or a json object (they're basically the same) would be optimal for this. And I supposed there is no limit to how much stuff you can fit into a cell.
        - the issue with jamming it all into a cell as some type of list is that the separator I *want* to use is a comma. But I don't think that will hold up too well in a .csv file, so we can workshop that.
        - my proposed dictionary (just a key:value pair data structure for you R people) would look like:
            - {2005: '723hiwa9y', 2006: 'sadpuyh3q4-98y', 2007: 'p9uh4bgf087', ...} where the random characters are the current IDs that are being used
    
### The problem of course, is how do we create a mapping from the current ID system to the new one...

some psuedo code to define the process:

```
load in the roster table I scraped, referred to as ROSTER

for each season of data
    yearROSTER = ROSTER[for that year]
    for each week
        weekROSTER = yearROSTER[for that week]
        for each game
            gameROSTER = weekROSTER[for that game]
            for each play
                - search through the play descriptions like "9-N.Foles pass complete to ..." to pull out strings like "9-
                    N.Foles" and then check to see if that exists in the NumberNameID column
                - if it does, verify that we have not yet assigned that player a DIFFERENT player's ID number
                    if the space if free, fill it
                    if not, print to a text file that there is an issue with those players and probably fill it by hand
                - if '9-N.Foles' does not appear in there, then we instead check in weekROSTER.
                - if that fails, add that player to a separate text file of issues.
                - if the filtering process returns multiple posible players that ID could match then thats an issue too
                    
```
will this work? Maybe not? I am not expecting it to work for a decent number of players. But if it does work for 
95% of players then we can always fill the rest in by hand. While that may take awhile, the benefits should theoretically be long lasting
