# Selenium WebDriver Checkboxes, Links, Dropdowns

### Checkboxes

        driver.get(“https://itera-qa.azurewebsites.net/home/automation”)

Select a specific checkbox from the list:

        driver.find_element(By.XPATH, “//input[@id='monday']”).click()

Select all the checkboxes from the list:

        checkboxes = driver.find_elements(By. XPATH, “//input[@type='checkbox' and contains(@id, 'day')]”)
          print(len(checkboxes))

Read checkboxes one by one:

Approach 1
  
		for i in range(len(checkboxes)):
			checkboxes[i].click()

Approach 2
  
		for checkbox in checkboxes:
			checkbox.click()

Let’s suppose I want to select several Checkboxes of choice. Eg, I want to select Monday and Sunday, or Tuesday and Friday. Based on my choice, I want to select multiple weekday names. Before clicking on a checkbox, I want to verify a condition. Make sure a weekday name matches an attribute value.

          for checkbox in checkboxes:
            weekday = checkbox.get_attribute(“id”)
            if weekday == “wednesday” or weekday == ”sunday”:
              checkbox.click()

I want to select the last few checkboxes from the end. What if I have 100 or 1000 checkboxes? There is a formula to handle it. First, I have to identify a start index:

__start_index = (total # of elements) - (# of elements to select from the end)__

__start_index = total_number_of_elements - 2__

        for i in range(len(checkboxes) - 2, len(checkboxes)):
          checkboxes[i].click()

Select the first 2 checkboxes:

        for i in range(len(checkboxes)):
          if i < 2:
            checkboxes[i].click()

Clear all checkboxes.
Suppose my requirement is to clear all the checkboxes in one shot. For both selecting and deselecting the same .click() command is used. If it’s selected it’ll deselect, if deselected it’ll select. Before clearing the boxes, verify if they are selected or not. I use the conditional method .is_selected():

           for checkbox in checkboxes:
            if checkbox.is_selected():
              checkbox.click()

### Links

The most frequently performed action on Links is __.click()__.

Links:
1) internal
2) external
3) broken link

- __Internal Link__ - when clicked, it navigates to the same page.
- __External Link__ - goes to some other web page when clicked.
- __Broken Link__ - the link if available on my page, but it does not have any target page. When I perform the _.click()_ action on a broken link, I don't get anything. Usually, developers keep broken links for future implementation.

Along with links, I also do some validation. On a web page, normally when I want to perform an action, I first identify the link and then perform the .click() action. 

Every link has an __href__ attribute. __href = Hyperlink REFerence__.

Identify the element using LINK_TEXT or PARTIAL_LINK_TEXT locator. Once I click the link, it automatically navigates to the target page. I use INK_TEXT most times. In PARTIAL_LINK_TEXT I pass a partial valye that can match some other elements.

Find the number of links in a page. I have to have one common attribute. All the links have one common __href__ attribute. But the attribute value itself is not the same for every link. So I cannot use any attribute to locate the links. Ny using the TAG_NAME locator, I can identify all of them, because the tagname is usually an anchor tag <a>. When I use the tagname of <a>, it returns all the elements on the web page. I use a common locator.

		links = driver.find_elements(By, TAB_NAME, "a")
		print("Total number of links:", len(links))
		
Or, use the Xpath locator:
		
		links = driver.find_elements(By, XPATH, "//a")

Now, I want to print the link names for all those links:

		for link in links:
			print(link.text)

Some names may be blanks, because a link doesn't have  any text in HTML.

__How to Handle a Broken Link__

In Java, I have to use different types of classes. But in Python it's very easy.

_Requirement:_ How many broken links are on a web page?

Most times, in a Production Application, I don't see any broken links. But I need to find out a specific page that contains any number of broken links.

Go to http://www.deadlinkcity.com/. A Broken Link doesn't have any target page. How do I find it? 

Whenever I open a link on a web page and send a Broken Link request, I receive back a Response Code. All the links with a broken code return code >= 400. Normal links always number less than 400. If the code < 400, I call those Response Codes. These are the normal valid links with proper resources on a web server. But if there's any Broken Link, all the Response Codes atart with 400. 400+ codes are Broken Links.

I verify how many (the total number of) broken links are on the page. Not only the total number, but also I display those broken links in the console window along with the normal links. Before that, I need to verify which link is brokem and which is normal.
	
First, I capture all the links on the web page. Then I read every link and open that link in the browser.
	
Another thing I can do is capture the _href_ attribute of every link (capture each link using its _href_ attribute). And then send a Request to the server by using that link. If I get an Error Code, a Status Response >= 400, I confirm it's a Broken Link. If it's not >= 400, I confirm it's a Valid Link.

But I do not manually open every link. Just like in automation, I do not test by opening each link in a new tab/window. I just capture the _href_ of the link and senf that link to the server.

How do I know, without opening a link, if I get an Error Code or not? Here I use something called the __Request Module__ from Python. I have to import/install that module, so that I can send a Request and receive a Response from the server. It's typically used for API testing, not for web testing. I utilize the Request Module to extract the _href_ attribute value. And then I send that value as a a part of the Request. I request something from the server by passing the URL (href attribute value). And the server gives a status back, called the Response. If the code is >= 400, there are no resources available on the web page.

This is related to the API kind of testing. I use the Requests Module to verify the Broken Links.
	
To install the built-in Requests Module:
	1. Go to  File > Settings > Project_name > Python Interpreter. 
	2. Click on the plus ➕ button.
	3. Type 'requests' in the search field.
	4. Install Package : Python HTTP for Humans
	
Also, import the 'requests' package:

	import requests as requests
	from selenium import webdriver
	from selenium.webdriver.common.by import By
	
	serv_obj = Service("...\chromedriver.exe")
	driver = webdriver.Chrome(service=serv_ojb)
	
	driver.get("http://www.deadlinkcity.com/")
	driver.maximize_window()
	
	# Capture all Links on web page
	all_links = driver.find_elements(By.TAG_NAME, "a")
	# Variable to count Broken Links later
	count = 0
	
	# For loop to read individual values one by one
	for link in all_links:
		url = link.get_attribute('href')
	
		try:
			response = requests.head()url
		except:	
			None
		
		if response.status_code >= 400:
			print(url, "is a Broken Link")
			count += 1
		else:
			print(url, "is a Valid Link")
	
	print("Total number of Broken Links:", count)
	
I have to hit the link, captured in the for loop, to the server and capture the status. Based on the status code number, I decide if it's a Broken or a Valid (normal) Link. From each link, I extract the _href_ attribute value, which gives me the actual URL. Send this URL to the server, and the server responds with the Response Status Code. To hit the Request to the server, the 'requests' module comes into picture. From the module I call the method _.head()_ and pass the URL into it. I store the server's Response in a variable called 'response'. Upon receiving the response, I verify the status code. If the code >= 400, the link is Broken. Else, the link is Valid. For each found Broken Link, I increment the 'count' by 1.
	
The 'requests' module does not click on the URL, open it in the browser, do any of the web operations. Because this is purely API testing related stuff. Internally, I'm passing this URL as a Request to (hit) the server.

Normally, when I get the URL, I directly open the browser. Manually, I send the Request through the Application. through the Browser. Also, I get my Response back only through the Browser. Eg, when I type 'google.com', I request the server to provide me with the google.com page. I ask the server to provide the Response as a web page by providing the URL. The server does not respond with the actual UI. It provides the response content, which can be viewed via Web Page > View Page Source.

But the 'requests' module hits the URL to the server from the backend, not frontend. I send the URL to the server directly through the 'requests' module. When I send the Request, sometimes the communication may happen from both the frontend and backend, or Client and Server. There are chances of _Network Exceptions_ getting thrown.  When working between Client and Server, these exceptions that return by default. To avoid them, only put the Response Statement in the try-catch block as an exception handler. Inside the 'except' block there's nothing, so put 'None'.

The actual 'request' module from Python is mostly used for API Testing.


### Dropdowns		

Dropdown is not a typical single web element. It's handled in a different way with the pre-defined Selenium Select() class. Dropdown is a _select_ tag element which contains multiple _option_ tag web elements. I can select one of the options. Can perform various operations on a dropdown.
	
I pass the dropdown element inside the Select() class. And for the Select() class, I create an object called 'drp_country'. I use this object to select the options from the dropdown.
	
Import the Select() class package:
	
	from selenium.webdriver.support.select import Select
	
There are many Select() options. The most popular one is the .select_by_visible_text() method. I specify which value I want from the dropdown. Visible text is whatever text is visible on the web page. The text values are case sensitive. Specify the value exactly as it shows on the page, eg, "Macau" (inner text between opening and closing tags).

Another method is _.select_by_valuue()_. 'value' is actually one of the attributes of the <option> tag. I can only see this value in HTML. Sometimes 'visible_text' and 'value' look the same, but they are not. 'value' means I have to capture it from HTML.

Another method is _.select_by_index()_. I can't capture this index anywhere in HTML, so I have to count it from the page itself.

Most of the time, I use the _.select_by_visible_text()_ method, because the 'visible_text' is alwayd there.
Sometimes, the 'value' attribute is absent, hence I can't use it then.
'index' is my assumption, I can give any index and select any option.




Suppose I want to find the total number of options that are available. Use another method _.options_:
	
	all_options = drp_country.options
	print(len(all_options))

Now I want to print all 242 options in my window. Again, I have to write a loop statement:
	
	for option in all_options:
		print(option.text)

_.text_ extracts the text value of each option.


Requirement / Interview Questions: Select a dropdown option without built-in Select() functions, such as:

	drp_country.select_by_visible_text("Macau")
	drp_country.select_by_value("10")
	drp_country.select_by_index("16")

Use _break_ to break out of the loop, since there's no need to iterate through the rest of the option.text values.

	for option in all_options:
		if option.text == "Macau":
			option.click()
			break

Whenever I work on a dropdown with a _select_ tag, these are the ways I can handle it. Sometimes I may not see the _select_ tag, but rather a _div_ or a _button_ tag. In those cases, I don't use the Select() class. I directly write one common Xpath for all options.

	all_options = driver.find_elements(By.XPATH, "//*[@id='input-country']/option")
