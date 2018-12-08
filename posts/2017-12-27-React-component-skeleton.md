---
title: My React component template
date: "2017-12-27"
category: 'react'
tags:
    - react
    - js
---


This post lists some React component skeleton code which I use daily in my current project.

## Skeleton of a React component

```js

import React, { PureComponent } from 'react';
import PropTypes from 'prop-types';
import { FormattedMessage, injectIntl, intlShape } from 'react-intl';

// styles
import withStyles from '../../decorators/with-styles';
import styles from './MyComponent.scss';

@injectIntl
@withStyles(styles)
export default class MyComponent extends PureComponent {
    static propTypes = {
        intl: intlShape,
        propA: PropTypes.bool,
        propB: PropTypes.func,
    };
    static defaultProps = {
        propA: false
    };

    state = {
    };

    render() {
        const { intl: { formatMessage } } = this.props;
        const { } = this.state

        const text= formatMessage({ id: 'text_id' });

        return (
            <div>
                Hello world!
            </div>
        );
    }
}

```

## A skeleton of test file.


```js

import React from 'react';
// Test framework
import chai, { expect } from 'chai';
import sinonChai from 'sinon-chai';
import chaiEnzyme from 'chai-enzyme';
// Test framework configuration
chai.use(sinonChai);
chai.use(chaiEnzyme());
import sinon from 'sinon';
import { shallow } from 'enzyme';
import proxyquire from 'proxyquire';

// Stubs
import { createClassDecorator } from '../../../../test/stubs/StubDecorator';
import { createStubComponent } from '../../../../test/stubs/StubComponent';

describe('MyComponent component', () => {
    let wrapper;
    let wrapperInstance;

    const sandbox = sinon.sandbox.create();
    const defaultProps = {
    };

    let MyComponent;

    beforeEach(() => {
        MyComponent = proxyquire.noCallThru().load('../MyComponent', {
            'react-intl': {
                FormattedMessage: createStubComponent('FormattedMessage')
            },
            '../../decorators/with-styles': createClassDecorator(),
            './MyComponent.scss': {
            }
        }).default;

        wrapper = shallow(<MyComponent {...defaultProps} />);
        wrapperInstance = wrapper.instance();
    });

    afterEach(() => {
        sandbox.reset();
    });

    describe('#render', () => {
         it('should render something', () => {
            wrapper.setProps({
                propA: true,
            });

            console.log('====================================', wrapper.debug());
        });
   });
});

```