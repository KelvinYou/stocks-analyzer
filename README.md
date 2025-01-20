# Bursa Malaysia Scraper

This Python program scrapes stock codes and company names from the Bursa Malaysia website's list of companies on the main market. It uses Selenium to automate the browser interactions and extract the required data.

## Features

- Extracts stock codes and company names from the table.
- Automatically navigates through all pages of the table.
- Handles pagination and stops when the last page is reached.
- Handles dynamic loading and waits for necessary elements to appear.

## Requirements

- Python 3.7 or later
- Google Chrome browser
- Chromedriver (compatible with your Chrome version)

## Installation

1. Clone or download this repository.
2. Install the required Python packages:

   ```bash
   pip install selenium
   ```
3. Ensure that the `chromedriver` executable is in your system's PATH or located in the same directory as this script.

## Usage

1. Update the `chromedriver` path in the script if necessary.
2. Run the program:

   ```bash
   python scraper.py
   ```
3. The program will open a browser, navigate to the Bursa Malaysia main market page, and scrape the data.
4. Extracted stock codes and company names will be printed to the console.

## Key Functions

### `get_last_page_number()`

Finds the last page number from the pagination list:

```python
def get_last_page_number():
    pagination = driver.find_element(By.CLASS_NAME, 'pagination')
    page_items = pagination.find_elements(By.CLASS_NAME, 'paginate_button')
    last_page = None

    # Iterate over the page items and find the last one with a page number
    for item in page_items:
        if item.text.strip().isdigit():
            last_page = int(item.text.strip())

    return last_page
```

### Main Loop

The program iterates through each page of the table, extracting stock codes and company names:

```python
while True:
    try:
        tbody = wait.until(EC.presence_of_element_located((By.TAG_NAME, 'tbody')))
        rows = tbody.find_elements(By.TAG_NAME, 'tr')

        for row in rows:
            try:
                stock_link = row.find_element(By.CLASS_NAME, 'company-announcement-link')
                stock_code = stock_link.get_attribute('href').split('=')[-1]
                name = stock_link.text.strip()

                codes.append(stock_code)
                names.append(name)
            except Exception as e:
                print(f"Error processing row: {e}")

        current_page = get_last_page_number()
        if current_page == last_page:
            print("Reached the last page.")
            break

        next_button = wait.until(EC.element_to_be_clickable((By.LINK_TEXT, 'Next')))
        driver.execute_script("arguments[0].click();", next_button)
        time.sleep(2)

    except Exception as e:
        print(f"Error during scraping: {e}")
        break
```

## Output

The program prints the following to the console:

- A list of stock codes.
- A list of company names.

Example:

```bash
Stock Codes: ['1234', '5678', ...]
Company Names: ['Company A', 'Company B', ...]
```

## Notes

- The program relies on specific element classes and IDs from the Bursa Malaysia website. Any changes to the website structure might require updates to the script.
- Ensure a stable internet connection while running the script.
- If the script encounters an issue with clicking elements, verify that the browser window is visible and the page is fully loaded.

## Troubleshooting

- **Element Click Intercepted**: Ensure the "Next" button is visible and not overlapped by other elements. Use JavaScript scrolling to bring the button into view.
- **Dynamic Loading**: Increase the WebDriver wait times if elements take longer to load.

## License

This project is open-source and available under the MIT License.

