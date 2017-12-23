React Native 0.52-RC 官方文档学习记录二
## Guides

### s1.Components and APIs

这篇文档是把常用的内置组件进行了一个分类展示。
当然，并不局限于使用内置的这些，因为有很多开发者分享创建的组件，你可以从npm注册的库里去找，也可以从[Awesome React Native](http://www.awesome-react-native.com/)去找
分类展示：
- 基础组件
	- [View](http://facebook.github.io/react-native/docs/view.html)
	- [Text](http://facebook.github.io/react-native/docs/text.html)
	- [Image](http://facebook.github.io/react-native/docs/image.html)
	- [TextInput](http://facebook.github.io/react-native/docs/textinput.html)
	- [ScrollView](http://facebook.github.io/react-native/docs/scrollview.html)
	- [StyleSheet](http://facebook.github.io/react-native/docs/stylesheet.html)
- 用户界面
	- [Button](http://facebook.github.io/react-native/docs/button.html)
	- [Picker](http://facebook.github.io/react-native/docs/picker.html)
	- [Slider](http://facebook.github.io/react-native/docs/slider.html)
	- [Switch](http://facebook.github.io/react-native/docs/switch.html)
- 列表
	- [FlatList](http://facebook.github.io/react-native/docs/flatlist.html)
	- [SectionList](http://facebook.github.io/react-native/docs/sectionlist.html)
- iOS组件
	- [ActionSheetIOS](http://facebook.github.io/react-native/docs/actionsheetios.html)
	- [AlertIOS](http://facebook.github.io/react-native/docs/alertios.html)
	- [DatePickerIOS](http://facebook.github.io/react-native/docs/datepickerios.html)
	- [ImagePickerIOS](http://facebook.github.io/react-native/docs/imagepickerios.html)
	- [NavigatorIOS](http://facebook.github.io/react-native/docs/navigatorios.html)
	- [ProgressViewIOS](http://facebook.github.io/react-native/docs/progressviewios.html)
	- [PushNotificationIOS](http://facebook.github.io/react-native/docs/pushnotificationios.html)
	- [SegmentedControlIOS](http://facebook.github.io/react-native/docs/segmentedcontrolios.html)
	- [TabBarIOS](http://facebook.github.io/react-native/docs/tabbarios.html)
- android组件
	- [BackHandler](http://facebook.github.io/react-native/docs/backhandler.html)
	- [DatePickerAndroid](http://facebook.github.io/react-native/docs/datepickerandroid.html)
	- [DrawerLayoutAndroid](http://facebook.github.io/react-native/docs/drawerlayoutandroid.html)
	- [PermissionsAndroid](http://facebook.github.io/react-native/docs/permissionsandroid.html)
	- [ProgressBarAndroid](http://facebook.github.io/react-native/docs/progressbarandroid.html)
	- [TimePickerAndroid](http://facebook.github.io/react-native/docs/timepickerandroid.html)
	- [ToastAndroid](http://facebook.github.io/react-native/docs/toastandroid.html)
	- [ToolbarAndroid](http://facebook.github.io/react-native/docs/toolbarandroid.html)
	- [ViewPagerAndroid](http://facebook.github.io/react-native/docs/viewpagerandroid.html)
- 其它
	- [ActivityInddicator](http://facebook.github.io/react-native/docs/activityindicator.html)
	- [Alert](http://facebook.github.io/react-native/docs/alert.html)
	- [Animated](http://facebook.github.io/react-native/docs/animated.html)
	- [CameraRoll](http://facebook.github.io/react-native/docs/cameraroll.html)
	- [Clipboard](http://facebook.github.io/react-native/docs/clipboard.html)
	- [Dimensions](http://facebook.github.io/react-native/docs/dimensions.html)
	- [KeyboardAvoidingView](http://facebook.github.io/react-native/docs/keyboardavoidingview.html)
	- [Linking](http://facebook.github.io/react-native/docs/linking.html)
	- [Modal](http://facebook.github.io/react-native/docs/modal.html)
	- [PixelRatio](http://facebook.github.io/react-native/docs/pixelratio.html)
	- [RefreshControl](http://facebook.github.io/react-native/docs/refreshcontrol.html)
	- [StatusBar](http://facebook.github.io/react-native/docs/statusbar.html)
	- [WebView](http://facebook.github.io/react-native/docs/webview.html)

### s2.Platform Specific Code

要做一个跨平台的应用，需要在不同平台上有不一样的方案。ReactNative提供了两种方法去轻松的分离不同平台的代码。
- 使用`Platform`模块
- 使用平台特有的文件扩展

##### 使用Platform模块
Platform引用
```
import {Platform} from 'react-native';
```
示例1，判断是否是iOS平台,是的话，高度值为200，不是的话高度值100
```
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

示例2，使用`Platform.select`方法，实现类似switch的效果
```
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'blue',
      },
    }),
  },
});
```

示例3，使用`Platform.select`还可以实现返回不同的组件
```
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```

示例4，检测android版本
```
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

示例5，检测iOS版本
```
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```


#####  使用特有的扩展名

当平台特定的代码太复杂时，需要分成不同的文件。React Native会检测文件后缀是否带有.ios和.android。然后根据不同平台加载对应的文件
例如：有BigButton.ios.js和BigButton.android.js 两个组件文件
你可以加载这两个文件如下：
```
const BigButton = require('./BigButton');
```
然后React Native自动加载对应的文件


### s3.Navigating Between Screens

一般应用很少有设计成只有一个页面的。而管理多个屏幕并负责转换的工作一般都交由一个叫导航器的组件
这部分内容介绍几个导航组件
1. `React Navigation`
   如果您刚开始使用导航，则可能需要使用React Navigation。 React Navigation提供了一个易于使用的导航解决方案，能够在iOS和Android上呈现常见的堆栈导航和选项卡式导航模式。由于这是一个JavaScript实现，当与状态管理库（如redux）集成时，它提供了最大的可配置性和灵活性。
   第一步，需要安装这个独立的库
	```
	npm install --save react-navigation
    ```
   然后可以很快的创建一个两个屏幕的应用
	```
	import {
		StackNavigator,
	} from 'react-navigation';
		
	const App = StackNavigator({
		Home: { screen: HomeScreen },
		Profile: { screen: ProfileScreen },
	});
	```
    
	设置导航选项
	```
	class HomeScreen extends React.Component {
		static navigationOptions = {
			title: 'Welcome',
		};
		render() {
			const { navigate } = this.props.navigation;
			return (
				<Button
					title="Go to Jane's profile"
					onPress={() =>
						navigate('Profile', { name: 'Jane' })
					}
				/>
			);
		}
	}
	```
	详细的内容要看 [React Navigation Getting Started Guide](https://reactnavigation.org/docs/intro/)专题

2. `NavigatorIOS`
   
    如果你仅是为了做iOS部分，那么可以使用`NavigatorIOS`，它提供了一个与原生一样效果的导航。
    
   	NavigatorIOS 看起来和感觉上很像UINavigationController,因为是在它基础上创建的。和其它导航系统一样，它也是使用了路线，但也有明显的不同。可以在路线里通过组件key来指定实际要显示的组件，要传递的属性可以通过passProps来传递。一个navigator对象会自动作为一个属性传递给组件，允许你使用pop和push.
	
	```
    import React from 'react';
	import PropTypes from 'prop-types';
	import {Button, NavigatorIOS, Text, View} from 'react-native';

	export default class NavigatorIOSApp extends React.Component {
		render() {
			return (
      			<NavigatorIOS
        			initialRoute={{
         				 component: MyScene,
          				 title: 'My Initial Scene',
                         passProps: {index: 1},
        			}}
        			style={{flex: 1}}
      			/>
    		);
		}
	}

	class MyScene extends React.Component {
		static propTypes = {
    		route: PropTypes.shape({
      			title: PropTypes.string.isRequired,
    		}),
    		navigator: PropTypes.object.isRequired,
		};
		constructor(props, context) {
			super(props, context);
			this._onForward = this._onForward.bind(this);
		}
		
		_onForward() {
			let nextIndex = ++this.props.index;
    		this.props.navigator.push({
      			component: MyScene,
      			title: 'Scene ' + nextIndex,
      			passProps: {index: nextIndex},
    		});
		}

		render() {
			 return (
      			<View>
        			<Text>Current Scene: {this.props.title}</Text>
       				 <Button
          				onPress={this._onForward}
         				 title="Tap me to load the next scene"
        			/>
      			</View>
    		);
			}
		}
	```


3. 如果你想实现一个和原生一样效果，且可以支持ios和android，你可以尝试以下两个库： [native-navigation](http://airbnb.io/native-navigation/), [react-native-navigation](https://github.com/wix/react-native-navigation)


### s4.
