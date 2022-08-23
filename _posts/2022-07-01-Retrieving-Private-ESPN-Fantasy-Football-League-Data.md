## Retrieving Private FF league info from ESPN

Code to import information from a private fanatast football league hosted in ESPN.

```python
# install the ff_espn_api
pip install ff_espn_api
```

```python
# import pandas as pd
import pandas as pd
```

```python
years = [] # years variable; enter years you want to retrieve, each year seperated by a comma
league_dict = {} # a league dictionary
for x in years:
    
    # Creating key name for dict
    key_name = 'league_'+str(x)  
    
    #ESPN API
    from ff_espn_api import League
    league_id =  #your league_id
    year = x
    swid = '{}' #your swid
    espn_s2 = '' #your espn_s2
    league = League(league_id, year, espn_s2, swid)
    
    # get a list of columns for our dataframe
    df_columns = list(league.teams[0].__dict__.keys())

    # remove roster from list of columns
    df_columns.remove('roster')
    
    # append teams to dataframe
    league_temp = pd.DataFrame()
    for d in range(len(league.teams)):
        team_df = pd.DataFrame(league.teams[d].__dict__, columns=df_columns)
        league_temp = league_temp.append(team_df)
        
    # number of rows
    n_rows = len(league_temp.index)
        
    # change the index to 'week'
    league_temp.index.names = ['week']

    # add 'week' column
    league_temp.reset_index(level='week', inplace=True)

    # add +1 to every week
    league_temp['week'] = league_temp['week'].apply(lambda x: x + 1)
    
    # add year column
    year = [x]*n_rows
    league_temp['year'] = year
    
    
    # drop unecessary columns
    league_temp = league_temp.drop(['division_id', 'team_abbrev',
                               'streak_length', 'streak_type', 'logo_url', 'standing',
                              'outcomes'], axis=1)
    
    league_dict[key_name] = league_temp
```


