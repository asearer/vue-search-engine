import React, { useState } from 'react';

// List of search engines
const searchEngines = [
    { name: 'Google', url: 'https://www.google.com/search?q=' },
    { name: 'Bing', url: 'https://www.bing.com/search?q=' },
    { name: 'DuckDuckGo', url: 'https://duckduckgo.com/?q=' },
    { name: 'Yahoo', url: 'https://search.yahoo.com/search?p=' },
    { name: 'Baidu', url: 'https://www.baidu.com/s?wd=' },
    { name: 'Yandex', url: 'https://yandex.com/search/?text=' },
    { name: 'Ask', url: 'https://www.ask.com/web?q=' },
    { name: 'AOL', url: 'https://search.aol.com/aol/search?q=' },
    { name: 'Wolfram Alpha', url: 'https://www.wolframalpha.com/input/?i=' },
    { name: 'Scholar Google', url: 'https://scholar.google.com/scholar?q=' },
    { name: 'Pinterest', url: 'https://www.pinterest.com/search/pins/?q=' },
    { name: 'Reddit', url: 'https://www.reddit.com/search?q=' },
    { name: 'Stack Overflow', url: 'https://stackoverflow.com/search?q=' },
    { name: 'IMDb', url: 'https://www.imdb.com/find?q=' },
];

const SearchEngineDropdown = () => {
    const [selectedEngine, setSelectedEngine] = useState(searchEngines[0].url); // Default to the first engine

    const handleChange = (event) => {
        const selected = event.target.value;
        const selectedUrl = searchEngines.find(engine => engine.name === selected).url;
        setSelectedEngine(selectedUrl);
    };

    const handleSearch = (query) => {
        // Redirect user to the selected search engine with the search query
        window.open(`${selectedEngine}${encodeURIComponent(query)}`, '_blank');
    };

    return (
        <div>
            <label htmlFor="search-engine">Choose a search engine:</label>
            <select id="search-engine" onChange={handleChange}>
                {searchEngines.map((engine, index) => (
                    <option key={index} value={engine.name}>
                        {engine.name}
                    </option>
                ))}
            </select>

            {/* Example search input and button to use selected search engine */}
            <div>
                <input
                    type="text"
                    placeholder="Search..."
                    onKeyDown={(e) => {
                        if (e.key === 'Enter') {
                            handleSearch(e.target.value);
                        }
                    }}
                />
                <button onClick={() => handleSearch(document.querySelector('input').value)}>Search</button>
            </div>
        </div>
    );
};

export default SearchEngineDropdown;
