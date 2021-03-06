<h1>SEI-Project Two: Reactathon</h1>

<h2>Project Brief</h2>

<p>This was the second project I completed in General Assembly’s Immersive Software Engineering course. Project two was pair project, where my pair and I were tasked with building a functional React Application that consumed a public API in a 48 hour time period. </p>

<h2>Technologies used</h2>
<ul>
<li>React JS</li>
<li>JavaScript</li>
<li>Axios</li>
<li>Bulma</li>
<li>Insomnia</li>
<li>Sass</li>
<li>Github</li>
<li>Netlify</li>
<li>Nomics Public API</li>
<li>VS Code Live Share</li>
</ul>

<h2>The App: Crypto Show</h2>

<h3>App Overview</h3>
<p>Crypto Show is a single page React Application with multiple components. The app consumes Nomics Public API and mounts the data back to the app. Where it is rendered in an interactive way for the user.</p>

You can find a live version of the app here: [Crypto Show](https://cryptoshow.netlify.app/)

<h3>How to use the App</h3>

<ol>
<li>When a user first logs onto the app they are taken to the home page. On the home page the Nav Bar is hidden and user can select one of three links below the heading.</li>

# ![](images/home-page.png)  



<li>If a user clicks, ‘See All Currencies’ they are taken to the page that corresponds to ‘coins’ in the Nav Bar. This page lists all the crypto currencies in the Nomics API. A user can search for a currency by name or scroll through the currencies page by page. The page is restricted to 100 results per page number. A user can click on a currency and will be taken to another page that displays more in depth historical data about that currency.</li>

# ![](images/coins.png) 

# ![](images/coin-card.png) 

<li>If a user clicks ‘I Could Have Made What!!!’ they are taken to the page that corresponds too ‘I Could Have Made What’ In the Nav Bar’. On this page a user can find out how much money they could have made from investing with either Bitcoin or Ethereum at a specific time.</li>

# ![](images/I-could-have-made-what.png) 

<li>Lastly if a user clicks ‘Whats Hot’ this corresponds to ‘Whats Hot’ in the Nav Bar a user is taken to the ‘Whats Hot’ page. At the top of the page are the embedded twitter profiles of the top 5 ranked crypto currencies on the Nomics API. Underneath that are the top ten biggest gainers and losers of the day. That are updated automatically every 10 seconds.</li>

# ![](images/whats-hot-1.png) 

# ![](images/whats-hot-2.png) 

</ol>

<h2>Creating The App</h2>

<p>Once we had a solid idea on the type of App we wanted to build we mapped out a rough visual representation of what pages the app should contain and look like using wireframes.<p>

# ![](images/wireframes/site_structure.jpg) 

# ![](images/wireframes/home-page.jpg) 

# ![](images/wireframes/i_could_have_made_what.jpg) 

# ![](images/wireframes/whats_hot.jpg) 


<h3>Home Page, Nav Bar and Site Structure</h3>

<p>We began the project by creating the site structure this meant creating all the components that would be used in the app. Each component had a basic function that returned its name so we could tell each component was displaying correctly. Once all components had been created they were grouped into folders and imported to App.js. For the site navigation we used ‘React Router DOM’. Once the Nav Bar was created and all the components were linked we moved on to the homepage. To stick to a clean modern design we chose to hide the Nav Bar on the home page and display the three main components of the app underneath the title. I was responsible for creating for creating the navigation bar and the home page.</p>

<h3>Coins / All Currencies Page</h3>

<p>The first interactive component we created was the coins page. We started by making an API request to the Nomics API. We used Axios to make the GET request. Once testing the app was receiving the data we mapped over the data to create a table of results. The coin page consisted of one component. The ‘CoinIndex’ that held the Nav Bar and the headings for each category in the table. Each time an object was mapped over it would create a new ‘CoinCard’ component for each currency. ‘CoinCard’ was a child component of the ‘CoinIndex’. Here Dougal(my team mate) was responsible for testing the initial GET request to the Nomics API. I was responsible for creating the, ‘Coin Index’, ’Coin Card’ and ‘Coin Show’ pages.</p>

<p>The code snippet below are the functions used on the, 'Coin Index' page.</p>

```
function CoinIndex() {
  const [coins, setCoins] = React.useState(null)
  const [pageNumber, setPageNumber] = React.useState(1)
  const [selectedName, setSelectedName] = React.useState('')

  function increase () {
    setPageNumber(pageNumber + 1)
  }
  function decrease() {
    setPageNumber(pageNumber - 1)
  }



  React.useEffect(() => {
    const getData = async() => {
      try {
        const { data } = await getAllCoins( pageNumber )
        const filterCurrencies = () => data.filter(currency => {
          return currency.name.toLowerCase().includes(selectedName.toLowerCase())
        })
        setCoins(filterCurrencies())
      } catch (err) {
        console.log(err)
      }
    }
    getData()
    const interval = setInterval(() => {
      getData()
    }, 10000)
    return () => clearInterval(interval)
  }, [selectedName, pageNumber])


  const handleKeyUp = (e) => {
    const selectedName = e.target.value
    setSelectedName(selectedName)
  }

``` 

 <p>At this point we realised we were only able to request 100 results at a time. To overcome this problem we added a function that would display page numbers at the bottom of the page. A user would start on page one. When a user clicked page 2, a new GET request was made for the next 100 results. Then we added a search function that allowed a user to search for a currency by name.</p>

 <p>Lastly we added a link to each ‘CoinCard’, the link would take a user to a new component called ‘CoinShow’. ‘CoinShow’ would make another GET request to Nomics but this time it would use the id of a crypto currency from the request made on the ‘CoinIndex’ page retrieve the data for the crypto currency that had been clicked. The data was then laid out on the page allowing a user to see more detailed historical data on the given currency.</p>

<h3>I Could Have Made What?</h3>

<p>For this page we made a form that would capture a users data. Once we tested we were capturing the user data correctly we made another function that would take the user data and convert it to the correct form required by the Nomics API to make a historical data GET request. If data was received back from the Nomics API it would be passed into state and and a conditional statement would be used to render a result underneath the form. Dougal was responsible for creating, ‘I Could Have Made What Page?</p>

<p>The code snippet below contains the function to calculate what a user could have made from either Bitcoin or Ethereum if they had invested at a particular time.</p>

``` 
async function CalculateOpportunityLoss(formdata) {

  const key = process.env.REACT_APP_MY_API_KEY

  const foundCurrency = []
  let i = 1
  while (foundCurrency.length < 1) {
    const { data } = await getAllCoinsUSD(i)
    const resultArray = (data.filter(coinObject => {
      return coinObject.name === formdata.currency
    }))
    if (resultArray.length > 0) {
      foundCurrency.push(resultArray)
    } else {
      i++
    }
  }

  const currencyId = (formdata) => {
    if (formdata.currency === 'Bitcoin') {
      return 'BTC'
    } else return 'ETH'
  }

  const relevantId = currencyId(formdata)
  const relevantStartDate = new Date(formdata.date)
  const formattedStartDate = relevantStartDate.toISOString()
  const relevantEndDate = new Date(formdata.date)
  const formattedEndDate = relevantEndDate.toISOString()

  const getHistoricValue = async () => {
    const { data } = await axios.get(`https://cors-anywhere.herokuapp.com/https://api.nomics.com/v1/exchange-rates/history?key=${key}&currency=${relevantId}&start=${formattedStartDate}&end=${formattedEndDate}` )
    return data[0].rate
  }
  getHistoricValue()

  const calcNumericalResult = async () => {
    const currentValue = foundCurrency[0][0].price
    const historicValue = await getHistoricValue()

    return ((parseFloat(currentValue) / parseFloat(historicValue)) * parseFloat(formdata.amountInvested))
  }

  return (
    await calcNumericalResult()
  )
}

```


<h3>Whats Hot?</h3>

<p>We first started by researching how we could embed the twitter profiles of the top 5 ranked crypto currencies on the page and found that an NPM package called ‘React Twitter Embed’ was the best way to do this. Then we made a new function that used the same GET request we had used on the ‘CoinIndex’ page. We took the data and made a new function that compared all the 1 day price changes and pushed them into an array. It then took the top to prices from that array and sliced it into another array and took the bottom 10 prices and sliced those into another array. The results were then displayed back to page. We then added a setTimeOut to the GET request so the data on the page would refresh every 10 seconds. On the ‘Whats Hot Page’ I was responsible for finding out how to and implement using the Twitter profiles of the top 5 cryptocurrencies and styling/layout of the page. Dougal was responsible for creating the ‘Biggest Gainers and Losers’ functionality.</p> 

<h2>Challenges</h2>
<p>We ran into a few challenges while building the Crypto Show App. The two biggest challenges would have were, firstly was converting the data captured from a user into a format that the Nomics API would accept. Secondly would have been some of the object keys used in the Nomics API had irregular naming conventions that conflicted with JavaScript like having a numeral at the start of a key name. It took a while to find a work around for this.</p>

<h2>Wins</h2>
<p>The biggest win for me was creating an end product that was really close to our intial plan. We did have to make a few comprises after finding out that working with a public API was not as straight forward as we initially thought.<p>
<p>This was the first pair coded project we had both undertaken. Dougal and I worked really well as a team and both brought different strengths to the table that allowed us to make a site that looked great and was packed full of functionality.</p>

<h2>Learnings</h2>
<ul>
<li>The importance of good naming conventions when creating an API.</li>
<li>Carefully read API documentation.</li>
<li>When working in a team, learn from your team mates strengths especially when it comes to areas where you’re lacking in knowledge or you feel uncomfortable.</li>
</ul>

<h2>Future Features</h2>
<p>If I had more time I would like to:</p>
<ul>
<li>Update 'I Could Have What!!' page to search for more than two currencies.</li>
<li>Add more content to the 'Whats Hot? page.</li>
 </ul>