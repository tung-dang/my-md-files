---
title: Some React performance take-away tips
date: "2017-12-28"
category: 'react'
tags:
    - react
    - js
    - performance
---

Here are some takeaway notes I have learned after optimizing React performance for some component in my current project.

### Should limit accessing some global browser properties which can make reflow.

We have a method reading `this.container.getBoundingClientRect().top` which causes reflow every time of calling `getBoundingClientRect()`

According to this document <https://gist.github.com/paulirish/5d52fb081b3570c81e3a>,`getBoundingClientRect` makes reflow in browser whenever it is called.

**Solution:**

- Double check to prevent it called many times in React component lifecycle
- Use *debounce* or *throttle* to prevent it called many unnecessary times.
- Cache result of `getBoundingClientRect()` so you do not read the value too many times.



```jsx

export default class PageTree extends PureComponent {
	state = {
		SimplePageTree: null,
	    pageTreeTopBoundary: 0,
      	hasScroll: false,
	};

    componentDidMount() {
        this._debouncedCalculateScrollableBoundaries();
    }

    componentDidUpdate() {
        this._debouncedCalculateScrollableBoundaries();
    }

    // this method makes reflow in layout
    // use `debounce` to avoid `_calculateScrollableBoundaries` is called many times.
    _calculateScrollableBoundaries = debounce(() {
        if (!this.container) {
            return;
        }

        // sets the top for the scrollable box
        this.setState({
            pageTreeTopBoundary: this.container.getBoundingClientRect().top,
            // defines if it should show the line above
            hasScroll: this.container.scrollHeight > this.container.offsetHeight
        });
    }, 300);

	render() {
      // ...
	}
}
```



### Should not create anonymous function unnecessarily.

Creating a new function every time it is called may be expensive in case the component is rendered in numerous number.

**Solution**: binding **this** context to create a new function only once in constructor.

*Instead of:*

```jsx
this.timer = setTimeout(() => {
    this._expandPageTree();
}, 500);
```



*Should be:*

```jsx
export default class PageTreeRow extends Component {
    constructor(...args) {
        super(...args);
        this._expandPageTree = this._expandPageTree.bind(this);
    }

    componentWillReceiveProps(nextProps) {
        ...
        this.timer = setTimeout(this._expandPageTree, 500);
    }

    _expandPageTree() {
      // ...
    }
}
```

Or:

```jsx
export default class PageTreeRow extends Component {
    componentWillReceiveProps(nextProps) {
        ...
        this._timer = setTimeout(this._expandPageTree, 500);
    }

    // create a method which always bind with `this`
    expandPageTree = () => {
      // ...
    };
}
```

### In selector, make sure return the same data type all the time

If we returned different data types for a prop, there were a chance that component was re-render because shallow comparison in `shouldComponentUpdate` will return `false` in this case. Let take an example:



```jsx
export const createSelectItemsSelector = () => createSelector(
    branch => branch,
    branch => {
        if (branch) {
            return {
                ...
                ids: branch.get('results', []) // may return a Map/List/OrderedMap for empty array. The 2nd parameter is returned when index is beyond the bounds of the Collection if in List or when Collection does not contain this key if in Map/OrderedMap.
                ...
            };
        } else {
            return {
                ...
                ids: [], // Array type.
                ...
            };
        }
    }
);
```

In above examples, **ids** props can return an empty array (**[ ]**) or an empty Immutable List object (**List([ ])**) sometimes. That causes a waste re-rendering. Here is a fixed version of above code:



### Should not use `{underscore | lodash }.isEqual`

Because `underscore | lodash.isEqual` performs a deep comparison, it can hurt performance. See more in [lodash doc](https://lodash.com/docs/4.17.4#isEqual)

**Solution**:

- Write a custom simple shallow comparison, ex: `isShallowEqual`
- Or find a library to help us.



```js
/**
 * Perform a quick shallow comparison between 2 objects. This can be used in shouldComponentUpdate.
 * @param obj1
 * @param obj2
 * @param {Array} omitProps - array of props which will be ingored to compare
 * @returns {boolean}
 */
export const isShallowEqual = (obj1, obj2, omitProps = []) => {
    if (obj1 === obj2) {
        return true;
    }

    const allKeys = union(keys(obj1), keys(obj2));

    for (const key of allKeys) {
        if (omitProps.indexOf(key) >= 0) {
            continue;
        }

        if (obj1[key] !== obj2[key]) {
            return false;
        }
    }

    return true;
};
```



### Should use reference comparison (`===`) for internal state

Because we always use [`setSate`](https://reactjs.org/docs/react-component.html#setstate) method to change internal state of the component and `setState` don't mutate the state and it's safe to do super quick & simple comparison (**this.state === this.nextState) **in ex:  in `shouldComponentUpdate.`

There is an alternative in next item, we can use a helper method like `isShallowEqual` above



### Should not create empty array or object literals in high traffic component container

If a component container is rendered thousand times in page, we can save some ms by avoid creating **[ ]** or **{ } **in container by declaring constant for the empty array or object. It will make shallow comparison quicker.

ex:

```js
const EMPTY_ARRAY = [];

const makeMapStateToProps = (state, ownProps) => {
  const pageTreeGoldProps = {
    webItems: isRight? data : EMPTY_ARRAY,
    ...
  };
};
```



### In `shouldComponentUpdate`, use `{lodash | underscore}.omit` to exclude some properties from comparison of `{lodash | underscore}.isEqual` may be worse.**

In the past I thought use   `_.omit` in `shouldComponentUpdate` to exclude some properties before doing comparison since I guested that way may be faster. However after I added some measurement, I saw using `_.omit` is slower than not using it in this case.

*Worse*

```jsx
shouldComponentUpdate(nextProps, nextState) {
    const toOmit = [ 'intl' ];

    const compareNextProps = omit(nextProps, toOmit);
    const compareThisProps = omit(this.props, toOmit);

    const shouldUpdate = (this.state !== nextState) || !isEqual(compareThisProps, compareNextProps);
    return shouldUpdate;
}
```

**Solution: ** we write a custom helper shallow comparison like `isShallowEqual`  above to allow pass a backlist props which are ignored when comparing.
