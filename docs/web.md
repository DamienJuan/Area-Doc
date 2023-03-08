Web Client
==========

Description
===========
The front end is developed with **[React](https://fr.reactjs.org/)** and using **[Material UI](https://mui.com/)**


Routing
=======
The web application is divided into 4 pages :  
- the home page, where you can register or login
- the my-area page, where you can create an AREA with the services you are subscribed to
- the services page, where you can subscribe to services
- the profile page, where you can manage your existing AREAs
```js
export default function App() {
  return (
    <BrowserRouter>
      <Header />
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/my-area" element={<MyArea />} />
        <Route path="/services" element={<Services />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/client.apk" component={ApkDownload} />
      </Routes>
    </BrowserRouter>
  );
}
```

Register
========
This code defines a function named register that takes in an optional formData object as its argument. It returns a Promise that resolves with a boolean value indicating whether the registration was successful or not.

Within the function, it makes a POST request to a registration endpoint /api/auth/register using the Axios library. It passes the formData object as the request body.

If the request is successful, the function extracts two properties from the response data using destructuring:

- accountData: an object containing data related to the user's account
- accessToken: a token used for authentication
The function then sets the accountData object in the state using setAccount, sets the accessToken in the state using setToken, and sets isLoggedIn to true using setIsLoggedIn. It also logs the accountData object to the console.

Finally, the function resolves the Promise with a value of true.

If the request fails, the function logs the error to the console using console.error. It then rejects the Promise with an error message. The error message is obtained from the error response data, if available, or from the error object's message property.
```js
const register = (formData = {}) =>
    new Promise((resolve, reject) => {
      axios
        .post('/api/auth/register', formData)
        .then(({
          data: {
            data: accountData,
            token: accessToken,
          },
        }) => {
          setAccount(accountData)
          console.log("AccountData->", accountData);
          localStorage.setItem('token', token);
          localStorage.setItem('username', accountData.username);
          localStorage.setItem('userid', accountData._id);
          setToken(accessToken)
          setIsLoggedIn(true)
          resolve(true)
        })
        .catch((error) => {
          console.error(error)
          reject(error?.response?.data?.message || error.message)
        })
    })
```

Login
=====
This code defines a function named login that takes in an optional formData object as its argument. It returns a Promise that resolves with a boolean value indicating whether the login was successful or not.

Within the function, it makes a POST request to a login endpoint /api/auth/login using the Axios library. It passes the formData object as the request body.

If the request is successful, the function extracts two properties from the response data using destructuring:

- accountData: an object containing data related to the user's account
- accessToken: a token used for authentication
The function then sets the accountData object in the state using setAccount, sets the accessToken in the state using setToken, and sets isLoggedIn to true using setIsLoggedIn. It also logs the accountData object to the console.

Finally, the function resolves the Promise with a value of true.

If the request fails, the function logs the error to the console using console.error. It then rejects the Promise with an error message. The error message is obtained from the error response data, if available, or from the error object's message property.
```js
const login = (formData = {}) =>
    new Promise((resolve, reject) => {
      axios
        .post('/api/auth/login', formData)
        .then(({
          data: {
            data: accountData,
            token: accessToken,
          },
        }) => {
          setAccount(accountData)
          localStorage.setItem('username', accountData.username);
          localStorage.setItem('userid', accountData._id);
          console.log("AccountDATA=", accountData);
          setToken(accessToken)
          setIsLoggedIn(true)
          resolve(true)
        })
        .catch((error) => {
          console.error(error)
          reject(error?.response?.data?.message || error.message)
        })
    })
```

Auth
====
ex : GitHub  
The component AuthGithub takes in a single props object. Within the component, it initializes a state variable isGithubAuth and sets its initial value to null. It also uses the useAuth hook, which likely returns authentication data related to the user's account.

The component also defines a function named sendGithubToServ, which takes in two arguments: an accessToken and a gitname. The function makes an asynchronous PUT request to a URL using the fetch function. The URL is http://localhost:8080/api/auth/github/githubat.

The request is made with the following options:

- The method is PUT.
- The headers include a Content-Type header with a value of "application/json".
- The body of the request is a JSON object with three properties: token, username, and gitname. The values of these properties are set to the accessToken, a value retrieved from localStorage.getItem("username"), and the gitname argument, respectively.  

If the request is successful, the function logs the response data to the console using console.log. If an error occurs, it logs the error to the console using console.error.
After the request completes (whether successfully or unsuccessfully), the function logs a message to the console saying that the fetch was successful.
It handle the authentication with a Github account, sending an access token and Github username to the server for verification.
```js
const AuthGithub = (props) => {
  const [isGithubAuth, setIsGithubAuth] = useState(null);
  const { account } = useAuth();

  const sendGithubToServ = async (accessToken, gitname) => {
    const requestOptions = {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        token: accessToken,
        username: localStorage.getItem("username"),
        gitname: gitname,
      }),
    };
    try {
      console.log("Fetching with '", accessToken, "' ...");
      await fetch(
        "http://localhost:8080/api/auth/github/githubat",
        requestOptions
      )
        .then((res) => res.json())
        .then((data) => {
          console.log("RESPONSE OF SEND DATA TO SERV:", data);
        });
    } catch (error) {
      console.error(error);
    }
    console.log("Fetched succesfully");
  };
```

Operating diagram :
![](/assets/web_diagram.png)
