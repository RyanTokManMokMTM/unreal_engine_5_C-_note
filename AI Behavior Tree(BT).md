## AI Behavior Tree(BT)

*How IT WORK?*

* **Behavior Tree(What AI Controller Does)**

* **Blackboard(A data container that is being used by BT)**
* **AI Controller run Behavior Tree when possessing**

### Behavior Tree

![tree](https://upload.cc/i1/2021/07/01/6jH9kc.png)

**This BT  will trigger every tick(0.02s),form top to bottom and left to right**

**Each Node is a task that BT will maintain. There are 3 main composition to run the task**

* Selector(This composition will choose either one sub-tree to maintain,)
* Sequence(This composition will handle its own tasks sequentially,if either one task is failed,then whole Sequence will be failed)
* parallel(This Composition will handle all task parallelly)

![composition](https://upload.cc/i1/2021/07/01/W8IGyP.png)

**Create Task(What AI Will do)**

![task](https://upload.cc/i1/2021/07/01/uPZ9zI.png)

*This task will only trigger by AI*

*2 Important Node*

![NodeAI](https://upload.cc/i1/2021/07/01/FHI1DS.png)

*This Event Node: Will Only Trigger by AI( When BT is running ,this task will receive the AI Controller that processing current BT)*

![NodeEnd](https://upload.cc/i1/2021/07/01/ctCsAn.png)

*This Node: When the task is done, is success or failed*



***Logic About The task above***

* BT Trigger the *Event Receive Execute AI* Node
* Get the AI Controller and Cast it to our Custom AI Controller Type
* In Our Custom  AI Controller, we have already set the controlling Pawn as a public variable, then we can get the Actor location
* Get the Random location in reachable point within out NavMeshBoundsVolume
* Set the value to Blackboard and the key will pass outside the task
* Finished the task

**Task Decorator(used to set decoration => set the condition before run the task)**

*example*

![condition](https://upload.cc/i1/2021/07/01/LUNijP.png)

*If the location less than -> Greater than ->Cooldown ->(All Condition is true) => run task *



**Task Service(used to set Service=> set the Service after run the task,what will AI extra think, focus on character ?? etc)**



### Blackboard(BT can only access data from BB. Blackboard is key value(JSON) pattern)

![BB](https://upload.cc/i1/2021/07/01/N7QSxd.png)

*Set value in AI Controller(need to get the blackboard)*

![BBValueInAICon](https://upload.cc/i1/2021/07/01/WKhYe1.png)

*On Target Perception Updated is AI Perception function ,trigger it when target perception is updated(in our case is AI see the character or not)*



*Set value in BT(no need to get the blackboard,just need to pass the key when using task)*

![TaskBB](https://upload.cc/i1/2021/07/01/0qvYn9.png)

*And passing the key when using in BT*

![get location](https://upload.cc/i1/2021/07/01/HcTIld.png)

![set ket](https://upload.cc/i1/2021/07/01/yXK4uS.png)

