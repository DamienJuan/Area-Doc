Description
===========
[React Native](https://reactnative.dev/) is a framework developed by Facebook teams in 2015 with the aim of accelerating the development and maintenance of its mobile applications. This technology allows developers to develop native iOS and Android mobile apps with a single code base while keeping the "look and feel" of native apps.

Prerequisites to use the mobile application
============================================
Node.js and npm must be installed on your computer.  
- Step 1: Open a terminal  
Open a terminal or a command line and access the project directory.  

- Step 2: Install the dependencies  
Run the following command to install the project's dependencies:
```bash
npm install
```
This command will install all the packages and dependencies needed to run your application.  

- Step 3: Launch the application  
To launch the application, run the following command:  
For Android: 
```bash
react-native run-android  
```
- Step 4: Test the application  
Once the application is launched, you can test the application's functionality on an emulator or mobile device.  

Navigation
==========

Navigation between pages is done using @react-navigation/native is a navigation library by providing navigation components to do this we use StackNavigator it is through this component that we can move between the different services offered:

```js
 const Navigation = () => {
  const isLog = AsyncStorage.getItem('isLog');
  const Stack = createStackNavigator();


  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Login" component={Login} options={{ headerShown: false }} />
        <Stack.Screen name="Register" component={Register} options={{ headerShown: false }} />
        <Stack.Screen name="Service" component={Service} options={{ headerShown: false }} />
        <Stack.Screen name="Area" component={Area} options={{ headerShown: false }} />
        <Stack.Screen name="Profil" component={Profil} options={{ headerShown: false }} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}; 
```

Resgister/Login
===============
In order to register, log in and make full use of the application the use of : 

- AsyncStorage: Allows to persist data locally on the smartphone, asynchronously and unencrypted.

- useState : Hook integrated in React that allows to manage the state of a functional component using the concept of "state". The state is an object that represents the current state of a component and can contain data that can be modified over time. The useState hook takes as initial parameter the initial value of the state and returns an array containing two elements: the value of the current state and a function to update this value.

- Axios: JavaScript library working as an HTTP client. It allows to communicate with APIs using requests. As with other HTTP clients, it is possible to create requests with the POST method.  

 
This allows us to connect to the server, to send and receive information, and to use the different services offered by our application

```js
const Login = ({navigation}) => {
    const [username, setUsername] = useState();
    const [password, setPassword] = useState();
    const [isLoggedIn, setIsLoggedIn] = useState(false); // add new state variable
    const serverEndpoint = 'http://<PUB-IP>:8080';

    const login = async () => {
        console.log(username, password);
        const newUser = {username: username, password: password, platform: "mobile"};
            await axios
              .post(`${serverEndpoint}/api/auth/login`, newUser)
              .then(({
                data: {
                  data: accountData,
                  token: accessToken,
                },
              }) => {
                console.log("AccountData->", accountData);
                console.log("AccessToken->", accessToken);
                AsyncStorage.setItem('token', accessToken);
                AsyncStorage.setItem('username', accountData.username);
                setIsLoggedIn(true);
                navigation.navigate("Service");
              })
              .catch((error) => {
                console.error(error)
              })
    } 
```

Architecture for mobile client :
![](/assets/Client/class_model.png)
