# Terribly-Tiny-Tales-assessment
1.On first load, only has a Submit button
2.On clicking on Submit, it will fetch the contents of https://www.terriblytinytales.com/test.txt
3.Parse the content and find the frequency of occurrence of each word (some words will occur only once, some twice and so on, and some will occur N times)
4.Then on the frontend, plot a histogram of the 20 most occurring words.
5.Also build an "Export" button, which when clicked will download a CSV file of the histogram data.




import React, { useState } from 'react';

function App() {
  const [histogramData, setHistogramData] = useState(null);

  const handleSubmit = async () => {
    const response = await fetch('https://www.terriblytinytales.com/test.txt');
    const text = await response.text();
    const wordCount = {};

    const words = text.split(/\s+/);

    for (const word of words) {
      if (wordCount[word]) {
        wordCount[word]++;
      } else {
        wordCount[word] = 1;
      }
    }

    
    const histogramData = Object.entries(wordCount)
      .map(([word, count]) => ({ word, count }))
      .sort((a, b) => b.count - a.count)
      .slice(0, 20);

    setHistogramData(histogramData);
  };

  const handleExport = () => {
 
    const csv = 'Word,Count\n' + histogramData.map(({ word, count }) => `${word},${count}`).join('\n');

   
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });

    
    const url = URL.createObjectURL(blob);

    const link = document.createElement('a');
    link.setAttribute('href', url);
    link.setAttribute('download', 'histogram.csv');
    link.style.display = 'none';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };

  return (
    <div>
      <button onClick={handleSubmit}>Submit</button>
      {histogramData && (
        <>
          <h2>Top 20 words:</h2>
          <ul>
            {histogramData.map(({ word, count }) => (
              <li key={word}>
                {word}: {count}
              </li>
            ))}
          </ul>
          <button onClick={handleExport}>Export</button>
        </>
      )}
    </div>
  );
}

export default App;
