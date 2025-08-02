# Naruto Video Games Web Scraper

A Python web scraper that extracts Naruto video game data from the Naruto Fandom Wiki, organizing games by gaming console platforms.

## Project Overview

This scraper navigates the complex HTML structure of the Naruto Fandom Wiki's video games list page to collect comprehensive gaming data across multiple console platforms. It intelligently distinguishes between console sections with games and navigation sections without games.

## Features

- **Multi-Platform Coverage**: Extracts games from 19+ gaming platforms including PlayStation, Xbox, Nintendo, and PC
- **Smart Navigation**: Uses DOM tree traversal to correctly associate console headers with their corresponding game tables
- **Data Cleaning**: Filters out header metadata to extract clean English game titles
- **Error Handling**: Properly handles sections without game tables (navigation sections, references, etc.)
- **Structured Output**: Organizes data into console-to-games mappings for easy analysis

## Technical Implementation

### Libraries Used
- `requests`: HTTP requests for web page retrieval
- `BeautifulSoup`: HTML parsing and DOM navigation
- `matplotlib`: Data visualization (optional)

### Key Algorithms
1. **HTML Tree Navigation**: Uses parent-child and sibling traversal to locate game tables
2. **Pattern Recognition**: Identifies table structure (every 5th cell = English game name)
3. **Boundary Detection**: Distinguishes between console sections and navigation sections

## Installation

```bash
pip install requests beautifulsoup4 matplotlib
```

## Usage

```python
import requests
from bs4 import BeautifulSoup

# Scrape the Naruto games wiki page
url = "https://naruto.fandom.com/wiki/List_of_video_games"
response = requests.get(url)
doc = BeautifulSoup(response.text, 'html.parser')

# Extract console names
consoles = doc.find_all('span', class_='mw-headline')

# Process each console and extract games
console_games = {}
for console in consoles:
    console_name = console.get_text()
    h2_parent = console.parent
    next_element = h2_parent.next_sibling
    
    # Navigate to find table or next console
    while next_element and next_element.name != 'table' and next_element.name != 'h2':
        next_element = next_element.next_sibling
    
    if next_element and next_element.name == 'table':
        # Extract English game names
        game_cells = next_element.find_all('td')
        english_games = []
        
        for i in range(0, len(game_cells), 5):
            english_name = game_cells[i].get_text().strip()
            english_games.append(english_name)
        
        console_games[console_name] = english_games
        print(f"\n{console_name}: {len(english_games)} games")
        for game in english_games:
            print(f"  - {game}")
    else:
        console_games[console_name] = []
        print(f"{console_name}: No games")
```

## Sample Output

```
WonderSwan Colour: 1 games
  - Naruto: Konoha Ninpōchō

Nintendo DS: 14 games
  - Naruto: Ninja Council 3
  - Naruto: Path of the Ninja
  - Naruto Shippūden: Ninja Council 4
  ...

Sony PlayStation 2: 8 games
  - Naruto: Ultimate Ninja
  - Naruto: Ultimate Ninja 2
  - Naruto: Ultimate Ninja 3
  ...

Data Carddass: No games
```

## Data Visualization

Optional matplotlib visualization to analyze game distribution:

```python
import matplotlib.pyplot as plt

# Filter consoles with games
consoles_with_games = {k: v for k, v in console_games.items() if len(v) > 0}
console_names = list(consoles_with_games.keys())
game_counts = [len(games) for games in consoles_with_games.values()]

# Create horizontal bar chart
plt.figure(figsize=(10, 8))
plt.barh(console_names, game_counts)
plt.xlabel('Number of Games')
plt.title('Naruto Games Distribution by Console')
plt.tight_layout()
plt.show()
```

## Results Summary

- **Total Consoles Analyzed**: 27 sections
- **Gaming Platforms with Naruto Games**: 19
- **Navigation/Reference Sections**: 8
- **Most Games**: Nintendo DS (14 games)
- **Data Source**: Naruto Fandom Wiki

## Project Structure

```
naruto-games-scraper/
├── naruto_scraper.py      # Main scraping script
├── README.md              # This file
└── requirements.txt       # Dependencies (optional)
```

## Learning Outcomes

This project demonstrates:
- Web scraping with BeautifulSoup
- HTML DOM tree navigation
- Data structure manipulation
- Pattern recognition in web content
- Error handling for inconsistent data
- Data visualization basics

## Future Enhancements

- Add release date analysis
- Include Japanese/Romaji name extraction
- Export data to CSV/JSON formats
- Add data validation and error logging
- Implement caching for repeated requests

## License

This project is for educational purposes. Respect the Naruto Fandom Wiki's terms of service and robots.txt when using this scraper.
