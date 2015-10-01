---
layout: post
title: "Memento Pattern in AtScript"
description: memento, design pattern, AtScript
headline:
category: development
tags: [memento, design pattern, AtScript]
comments: true
mathjax:
---



In this example we will be talking about one of the behavioral pattern which is Memento. The memento pattern provides the ability to capture and externalize an object's internal state for the object is to be restored to this state later without violating encapsulation. There are 3 actors in this pattern which are originator, caretaker and memento. In implementation memento does not have explicit mission. To be more precise, it is the type that two classes can communicate with. Originator is some object that has an internal state. Caretaker handles the storing and restoring processes via memento type. It waits for memento object from originator. Then it does whatever originator wants. Then it returns memento object to the originator. And originator uses it whatever code requires so.

According to [dofactory](http://www.dofactory.com/net/memento-design-pattern), frequency of use is low for this pattern.

Here is the implementation in [AtScript](https://docs.google.com/document/d/11YUzC-1d0V1-Q3V0fQ7KSit97HnZoKVygDxpWzEYW0U/edit) for this pattern(memento.ats):

```javascript
import { Logger } from '../logger';
import { int, float } from '../lang';

export class Originator {
    constructor() {
        this.logger = new Logger();
    }

    setState(_state:string) {
        this.state = _state;
    }

    getState():string {
        return this.state;
    }

    getStateFromMemento(memento:Memento) {
        this.state = memento.getMementoState();
    }

    saveStateToMemento():Memento {
        return (new Memento(this.state));
    }

}
export class Memento {
    constructor(_state:string) {
        this.mementoState = _state;
    }

    getMementoState():string {
        return this.mementoState;
    }

}
export class CareTaker {
    constructor() {
        this.mementoList = [];
    }

    addStateToMementoList(_state:Memento) {
        this.mementoList.push(_state);
    }

    getStateFromMementoList(index:int):Memento {
        return this.mementoList[index];
    }
}
```
Here is runner.ats to run memento.ats:

```javascript
import {Originator,CareTaker} from './memento';
import { Logger } from '../logger';

var originator = new Originator();
var careTaker = new CareTaker();
var logger = new Logger();
originator.setState("State #1");
originator.setState("State #2");
careTaker.addStateToMementoList(originator.saveStateToMemento());

originator.setState("State #3");
careTaker.addStateToMementoList(originator.saveStateToMemento());
originator.setState("State #4");
logger.log("Current state is: " + originator.getState());
originator.getStateFromMemento(careTaker.getStateFromMementoList(0));
logger.log("First saved state is: " + originator.getState());
originator.getStateFromMemento(careTaker.getStateFromMementoList(1));
logger.log("Second saved state is: " + originator.getState());

```

Here is the output:

```
Current state is: State #4
First saved state is: State #2
Second saved state is: State #3
```

Also you can find source code for this example and some other design patterns examples [here](https://github.com/ozzimpact/design-patterns-in-atscript), written in AtScript.
