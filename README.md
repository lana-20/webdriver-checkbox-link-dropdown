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
