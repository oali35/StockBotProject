This program is used to attain real time stock data from the IEX finance api and post statements to a groupme bot
which then spams the messages to a group chat. It is a great way to get kicked out of group chats due to spaming
and it also a great tool to help you realize if you are losing money or not if you invested into one of the companies
in the S&P 500.


What you will need to have:
1) You will need python installed on your computer. Specifcally 3.6+.
2) You will need pip3 or pip
3) You will need to pip install iexfinance
4) You will need to make a groupMe account
5) Once you make the group me account go to "https://dev.groupme.com" sign in
and follow the instructions to make the bot, add the bot to a group chat of your choosing and get the id to insert in a textfile.
6) Make sure the code, the textfile are all in the same directory or folder.
-- for conveniece sake a id.txt file is added so that you wont have to make a new file.
-- Happy spamming and stocking!

DOCUMENTATION:

--------------------------------------------------------------------------
def save_company_names():
    ticker = [] -> is an empty list
    
    append = ticker.append -> this way of appending to an empty
              list saves you execution time
              
    response =
        -- gets a response from the webpage
        
    soup = reads the text from the webpage
    
    table = beautiful soup api finds the html table element
    under the class name 'wikitable sortable'
    
    for row in table.findAll('tr')[1:]:
        iterates through every single tag with a tr
        except the 0th one
        
        ticker = row.findAll('td')[0].text;
        finds all the tags with the name 'td'
        and takes the 0th coloumn which contains the
        stock ticker for every company

        append(ticker)
        every ticker is then appended to the
        empty ticker list
        
    return(tickers)
    the function then returns the ticker
--------------------------------------------------------------------------

--------------------------------------------------------------------------
def get_stock_information(num):
    - this function takes in the number of companies you would like to get stock info
    of and uses the iexfinance api to return dictionaries which are then appended
    to the info empty list
    - the function then returns the empty list
    
    
    tickers = save_company_names()

    
    num = int(num)

    
    a = 0
    info = []
    
    append = info.append
    
    for i in tickers:
    
        if a < num:
            i = str(i)
            
            tick = i.replace("-", ".")
            this replace function was used because the api
            had trouble with reading hyphens due the wikipedia page
            scraping in the tickers and some tickers containing hyphens
            
            firm = Stock('%s' % tick)
            
            quote = firm.get_quote()
            
            append(quote)
            
            #return()
        a += 1
    #print(info)
    
    return(info)
--------------------------------------------------------------------------

--------------------------------------------------------------------------
class stock_Statements:
    def __init__(self, **kwargs):
        self.name = kwargs.setdefault('companyName', None)
- this sets the key of companyName in the dictionary passed through
and it sets it equal to a self.name
        
        self.tick = kwargs.setdefault('symbol', None)
- this sets the key of symbol in the dictionary passed through
and it sets it equal to a self.tick for the ticker name
        
        self.open = kwargs.setdefault('open', None)
- this sets the key of open in the dictionary passed through
and it sets it equal to a self.open for the opening price
        
        self.close = kwargs.setdefault('close', None)
- this sets the key of close in the dictionary passed through
and it sets it equal to a self.close for the closing price
        
        self.recentPrice = kwargs.setdefault('latestPrice', None)
- this sets the key of latestPrice in the dictionary passed through
and it sets it equal to a self.recentPrice for the most recent price
        
        self.time = kwargs.setdefault('latestTime', None)
- this sets the key of latestTime in the dictionary passed through
and it sets it equal to a self.time for the most recent time
        
        self.type = kwargs.setdefault('sector', None)
- this sets the key of sector in the dictionary passed through
and it sets it equal to a self.type for the type of industry the company is in
ex: tesla is in the automotive industry
    #
    
    def calculation(self):
        increase = self.recentPrice - self.open
- subtracts the recent price from the opening price for the numerator
        
        testO = increase/self.open
-divides increase price above the opening price to get increasing rate of return
if testO is negative it means it is a negative return
        
        precent = round(( testO * 100), 3)
-this function returns about 3 decimal places for accuracy
        
        return precent
- this finally returns the function

    def statement(self, firm_name, percentage):
        good = int(1)
        -provides a constraint so that if a value is above 1% or another positive integer it
        will be printed out
        
        bad = int(-1)
        -provides a constraint so that if a value is below -1% or another negative integer it
        will be printed out
        
        try:
        - runs every value through this algorithm that dictates wether or not the company will
        be printed
        
            if ((percentage < good and percentage > 0) or (percentage > bad and percentage < 0)):
                return(None)
            else:
                if percentage > good:
                    good_return = "{0} made a positive return of: % {1}".format(firm_name, percentage)
                    append(good_return)
                    return(good_return)
                elif percentage < bad:
                    bad_return = "{0} made a negative return of: % {1}".format(firm_name, percentage)
                    append(bad_return)
                    return(bad_return)
            #return(gb_list)
        except ValueError:
            return("Proper Values are not in company name and/or no precentage") 
--------------------------------------------------------------------------


--------------------------------------------------------------------------
def post_message(mess, the_id):
- this method uses the requests api and posts to the groupme bot

    try:
        groupMeID = '{0}'.format(the_id)#insert Group Me ID
        idGm = { 'bot_id' : groupMeID}
        dataa = {'text': mess}
        message = r.post("https://api.groupme.com/v3/bots/post", params = idGm, data = dataa)
        return(message)
    except urllib.error.HTTPError and requests.exceptions.HTTPError:
        return None
--------------------------------------------------------------------------

--------------------------------------------------------------------------
def post_message_subprocess(mess, the_id):
-- this method uses the subprocessing api and posts to the groupme bot

    try:
        groupMeID = '{0}'.format(the_id)#insert Group Me ID
        m = urllib.parse.quote_plus(mess, safe='_.-~')
        command = 'curl -X POST \"https://api.groupme.com/v3/bots/post?bot_id='+ groupMeID +'&text=' + m + '\"'
        return(subprocess.run(command))
    except urllib.error.HTTPError and requests.exceptions.HTTPError:
        return None
--------------------------------------------------------------------------

--------------------------------------------------------------------------
def config_file(file_name):
-- this takes in a config file with the group me bot ID
-- you will need to make a config file and put your bot id in the file
-- the programm will acknowledge the 1st argument as the file name

    arg_file = "{0}".format(file_name)
    
    with open(arg_file, 'r') as file:
    
        try:
            for i in file:
                gc_id = "{0}".format(i)
                #print(gc_id)
                return(gc_id)
        except FileNotFoundError:
            print("Argument was not accepted as file name")
            sys.exit()
        #return(gMe)
--------------------------------------------------------------------------

--------------------------------------------------------------------------
def looping_execution(amount_companies):
    takes in the amount of companies you would like to analyze
    and only analyzes those companies
--------------------------------------------------------------------------
main():
--- the main function takes everything in and uses the input to address
how many company's fromm the S&P 500 you would like to post.

----IMPORTANT NOTES----
1) depending on prefrence make sure you only use one method of the posting strategy
DO NOT USE BOTH SUB PROCCES POSTING AND REQUEST POSTING FUNCTIONS

2) Keep in mind there is no ranking system in the program
every single firm is posted alphabetically.

WINDOWS execution:

1) open up command prompt change to the directory the folder is with program
type "python {program name} {file name}"
ex: "python StockBot.py id.txt"

Linux execution:

1) 
"./{program name} {file name}"
ex: "./StockBot.py id.txt"
