from flask import Flask, request, render_template
import requests
from bs4 import BeautifulSoup

app = Flask(__name__)

def get_fitness_suggestions(query):
    search_url = f"https://www.google.com/search?q={query}"
    headers = {"User-Agent": "Mozilla/5.0"}
    response = requests.get(search_url, headers=headers)
    soup = BeautifulSoup(response.text, "html.parser")
    results = []
    for g in soup.find_all('div', class_='BNeawe vvjwJb AP7Wnd')[:5]:
        results.append(g.get_text())
    return results if results else ["No suggestions found"]

@app.route('/', methods=['GET', 'POST'])
def index():
    suggestions = []
    if request.method == 'POST':
        weight = float(request.form['weight'])
        if weight > 80:
            query = "diet and exercises needed for weight loss"
        else:
            query = "diet and exercise to be done for muscle gain"
        suggestions = get_fitness_suggestions(query)
    return render_template('index.html', suggestions=suggestions)

if __name__ == '__main__':
    app.run(debug=True)
