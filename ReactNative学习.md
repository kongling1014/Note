React Native 0.52-RC 官方文档学习记录

## The Basics 

### s1.props
许多组件在create时，可以通过`props`来指定不同的参数。比如下面的一个例子，`Image`组件，创建时可以通过`source`属性来指定它要加载的图片资源
```
import React, { Component } from 'react';
import { AppRegistry, Image } from 'react-native';

export default class Bananas extends Component {
  render() {
    let pic = {
      uri: 'https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg'
    };
    return (
      <Image source={pic} style={{width: 193, height: 110}}/>
    );
  }
}
```
**注意，在JSX语句中，`pic`变量要放在花括号中，花括号里可以放任意的JavaScript表达式**

你自己的组件里也可以使用`props`，只需要在`render`方法中引用`this.props`就可以。以下是一个例子
```
import React, { Component } from 'react';
import { AppRegistry, Text, View } from 'react-native';

class Greeting extends Component {
  render() {
    return (
      <Text>Hello {this.props.name}!</Text>
    );
  }
}

export default class LotsOfGreetings extends Component {
  render() {
    return (
      <View style={{alignItems: 'center'}}>
        <Greeting name='Rexxar' />
        <Greeting name='Jaina' />
        <Greeting name='Valeera' />
      </View>
    );
  }
}
```

### s2.State
有两种类型的数据可以控制一个组件：`props`和`state`.`props`在组件的整个生命周期里是固定的，对于将要改变的数据，我们必须使用`state`。
一般情况下，你需要在构造函数中对`state`进行初始化，并在需要改变时调用`setState`方法
举个例子，我们想要实现一个文字一直闪烁的组件，文字内容是在组件创建时就固定的，所以文字内容是一个`props`，文字在闪烁过程中是显示还是隐藏，这个就要用`state`来控制
```
import React, { Component } from 'react';
import { AppRegistry, Text, View } from 'react-native';

class Blink extends Component {
  constructor(props) {
    super(props);
    this.state = {showText: true};

    // Toggle the state every second
    setInterval(() => {
      this.setState(previousState => {
        return { showText: !previousState.showText };
      });
    }, 1000);
  }

  render() {
    let display = this.state.showText ? this.props.text : ' ';
    return (
      <Text>{display}</Text>
    );
  }
}

export default class BlinkApp extends Component {
  render() {
    return (
      <View>
        <Blink text='I love to blink' />
        <Blink text='Yes blinking is so great' />
        <Blink text='Why did they ever take this out of HTML' />
        <Blink text='Look at me look at me look at me' />
      </View>
    );
  }
}
```
实际应用中，可能不会通过一个定时器去设置state，一般会在数据更新后，或是用户输入后。也可以使用`Redux`这种状态容器来管理你的数据流。
`setState`一旦被调用，应用就会重新渲染组件。state的工作方式和React中是一样的，可以参考[React.Component API](https://reactjs.org/docs/react-component.html#setstate)

### s3.Style
在React Native中，你不需要使用一种特殊的语言或是符号去定义样式，只需要使用JavaScript就可以。所有的核心组件都支持`style`的属性。
样式的name和values除了名字使用驼峰命名方式之外，其它的和CSS一样
当组件变的越来越复杂时，可以使用`StyleSheet.create`把样式定义在一起，下面是一个例子：
```
import React, { Component } from 'react';
import { AppRegistry, StyleSheet, Text, View } from 'react-native';

export default class LotsOfStyles extends Component {
  render() {
    return (
      <View>
        <Text style={styles.red}>just red</Text>
        <Text style={styles.bigblue}>just bigblue</Text>
        <Text style={[styles.bigblue, styles.red]}>bigblue, then red</Text>
        <Text style={[styles.red, styles.bigblue]}>red, then bigblue</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  bigblue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  },
  red: {
    color: 'red',
  },
});
```

### s4.Height and Width

定义组件的大小，
- 固定尺寸
  最简单的办法是直接指定一个宽高值，在React Native中,宽高值都是没有单位的，代表设备密度density-independent pixels.在样式中直接使用`width`和`height`
  示例：
  ```
	import React, { Component } from 'react';
	import { AppRegistry, View } from 'react-native';
	export default class FixedDimensionsBasics extends Component {
		render() {
    		return (
      			<View>
        			<View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        			<View style={{width: 100, height: 100, backgroundColor: 'skyblue'}} />
        			<View style={{width: 150, height: 150, backgroundColor: 'steelblue'}} />
      			</View>
    		);
		}
	}

  ```
- 柔性尺寸flex
  在样式中使用flex，可以动态的放大和缩小组件大小。如果父容器中只有一个子view，子view的1代表着占满所有父容器。多个子view时，可以理解为是权重，值大的，占的就多。比如下面示例中的，1，2，3第一个View占1/6.
  **注意：如果父view没有定指width,height或是flex，那么父view的尺寸为0，flex类型的子view将不显示**
  示例：
  ```
	import React, { Component } from 'react';
	import { AppRegistry, View } from 'react-native';
	export default class FlexDimensionsBasics extends Component {
		render() {
    		return (
      // Try removing the `flex: 1` on the parent View.
      // The parent will not have dimensions, so the children can't expand.
      // What if you add `height: 300` instead of `flex: 1`?
      		<View style={{flex: 1}}>
      		  <View style={{flex: 1, backgroundColor: 'powderblue'}} />
      		  <View style={{flex: 2, backgroundColor: 'skyblue'}} />
      		  <View style={{flex: 3, backgroundColor: 'steelblue'}} />
      		</View>
    		);}}
  ```

### s5.Layout with Flexbox

组件可以通过flexbox算法来指定它的子view们的一个布局，`FlexBox`是用来在不同尺寸的屏幕上提供一个一样的布局效果
通常组合使用`flexDirection`,`alignItems`,`justifyContent`来实现正确的布局
**注意：`flexDirection`默认值为column **
- Flex Direction
	
	决定排列的主坐标方向
	```
	<View style={{flex: 1, flexDirection: 'row'}}>
	```

- justify Content
	
    指定多个子View的在主坐标方向上排列方式，可选值有`flex-start`, `center`, `flex-end`, `space-around`, 和 `space-between`.
    ```
	<View style={{flex: 1,flexDirection: 'column',  justifyContent: 'space-between',
      }}>
	```

- Align Items
	
	指定多个子view的在次坐标方向上排列方式，可选值有`flex-start`, `center`, `flex-end`, and `stretch`
	**注意：stretch要想起作用，则这个view不能在次坐标方向上使用固定值**

	```
     <View style={{
        flex: 1,
        flexDirection: 'column',
        justifyContent: 'center',
        alignItems: 'center',
      }}>
	```

### s6.Handling Text Input

`TextInput`是一个允许用户输入文字的基础组件。它有`onChangeText`prop和`onSubmitEditing`prop。`onChangeText`可以在每当文件改变时调用一个方法，`onSubmitEditing`可以文字提交时调用一个方法。
下面的例子是一个输入文本后，根据空格拆分，每个词换成一角比萨
```
import React, { Component } from 'react';
import { AppRegistry, Text, TextInput, View } from 'react-native';

export default class PizzaTranslator extends Component {
  constructor(props) {
    super(props);
    this.state = {text: ''};
  }

  render() {
    return (
      <View style={{padding: 10}}>
        <TextInput
          style={{height: 40}}
          placeholder="Type here to translate!"
          onChangeText={(text) => this.setState({text})}
        />
        <Text style={{padding: 10, fontSize: 42}}>
          {this.state.text.split(' ').map((word) => word && '🍕').join(' ')}
        </Text>
      </View>
    );
  }
}
```
onChangeText方法中设置State利用setstate重新渲染的机制刷新界面。

### s7.Handling Touches

用户使用移动设备时和应用交互最多的方式就是触摸。他们可以通过一系列组合的手势，比如点Button，滚动list，放大缩小地图。React Native提供了一套完整的手势处理组件，例如[gesture responder system](http://facebook.github.io/react-native/docs/gesture-responder-system.html)可以识别更高级的手势。不过你最关心的可能是基础的button

- 显示一个基础的Button
  ```
	<Button
		onPress={() => {
    		Alert.alert('You tapped the button!');
		}}
	title="Press Me"
	color="#841584"
	/>
  ```

> Generally, you can use **`TouchableHighlight`** anywhere you would use a button or link on web. The view's background will be darkened when the user presses down on the button.


> You may consider using **`TouchableNativeFeedback`** on Android to display ink surface reaction ripples that respond to the user's touch.


> **`TouchableOpacity`** can be used to provide feedback by reducing the opacity of the button, allowing the background to be seen through while the user is pressing down.


> If you need to handle a tap gesture but you don't want any feedback to be displayed, use **`TouchableWithoutFeedback`**.


### s8.Using a ScrollView

ScrollView是一个可滚动的容器，可以包含多个组件和view。通过设置`horizontal`可以进行横向滚动
示例：
```
<ScrollView>
          <Text style={{fontSize:96}}>Scroll me plz</Text>
          <Image source={require...
			...
</ScrollView>
```

### s9.Using List Views

React Native 提供了一系列可以展示数据列表的组件，通常，你会使用`FlatList`或`SectionList`.
FlatList展示的组件item之间有相似的结构，而且数据可能会随着时间改变，它与ScrollView不同的是，它只是渲染当前显示的item,而scrollView会渲染全部
FlatList需要两个prop：`data`和`renderItem`。data是数据源，renderItem是返回一个组件的渲染
以下是一个例子：
```
import React, { Component } from 'react';
import { AppRegistry, FlatList, StyleSheet, Text, View } from 'react-native';

export default class FlatListBasics extends Component {
  render() {
    return (
      <View style={styles.container}>
        <FlatList
          data={[
            {key: 'Devin'},
            {key: 'Jackson'},
            {key: 'James'},
            {key: 'Joel'},
            {key: 'John'},
            {key: 'Jillian'},
            {key: 'Jimmy'},
            {key: 'Julie'},
          ]}
          renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
        />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
   flex: 1,
   paddingTop: 22
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
})
```
如果是Item里带标题的，可以使用`SectionList`
```
 <SectionList
          sections={[
            {title: 'D', data: ['Devin']},
            {title: 'J', data: ['Jackson', 'James', 'Jillian', 'Jimmy', 'Joel', 'John', 'Julie']},
          ]}
          renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
          renderSectionHeader={({section}) => <Text style={styles.sectionHeader}>{section.title}</Text>}
          keyExtractor={(item, index) => index}
        />
```


### s10.Networking

大部分的移动应用都需要从远程的URL获取数据，你可能想创建一个POST请求，又或者是想直接从服务器请求一块静态资源。ReactNative提供了一个叫Fetch的API可以获取网络数据
如果想随便从一个网址获取内容，可以这么写
```
fetch('https://mywebsite.com/mydata.json');
```
如果需要进一步的设置，可以通过以下方式
```
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  }),
});
```
接下来是怎么处理响应
```
function getMoviesFromApiAsync() {
  return fetch('https://facebook.github.io/react-native/movies.json')
    .then(response => response.json())
    .then(responseJson => {
      return responseJson.movies;
    })
    .catch(error => {
      console.error(error);
    });
}
```
也可以按照ES2017的建议使用async/await语法
```
async function getMoviesFromApi() {
  try {
    let response = await fetch(
      'https://facebook.github.io/react-native/movies.json'
    );
    let responseJson = await response.json();
    return responseJson.movies;
  } catch (error) {
    console.error(error);
  }
}
```
接下来是一个完整的例子，从网络上获取一个json串，显示到List中
```
import React, { Component } from 'react';
import { ActivityIndicator, ListView, Text, View } from 'react-native';

export default class Movies extends Component {
  constructor(props) {
    super(props);
    this.state = {
      isLoading: true
    }
  }

  componentDidMount() {
    return fetch('https://facebook.github.io/react-native/movies.json')
      .then((response) => response.json())
      .then((responseJson) => {
        let ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
        this.setState({
          isLoading: false,
          dataSource: ds.cloneWithRows(responseJson.movies),
        }, function() {
          // do something with new state
        });
      })
      .catch((error) => {
        console.error(error);
      });
  }

  render() {
    if (this.state.isLoading) {
      return (
        <View style={{flex: 1, paddingTop: 20}}>
          <ActivityIndicator />
        </View>
      );
    }

    return (
      <View style={{flex: 1, paddingTop: 20}}>
        <ListView
          dataSource={this.state.dataSource}
          renderRow={(rowData) => <Text>{rowData.title}, {rowData.releaseYear}</Text>}
        />
      </View>
    );
  }
}
```


