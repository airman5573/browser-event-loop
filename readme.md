# 브라우저의 이벤트 루프

이벤트 루프관련 자료 모음입니다.  
발표자료 : https://drive.google.com/file/d/1DysiR8lffmCbN6SanAnjHNmGPOul9t_K/view?usp=sharing

## 테스트 코드
- [MacroTask로 일을 쪼개서 하기](https://jsfiddle.net/MR_RPF/wd2qtkx0/8/)
- [MicroTask 사용 예제](https://jsfiddle.net/MR_RPF/etrLkxbp/5/)
- [AnimationFrameQueue vs MacroTaskQueue](https://jsfiddle.net/MR_RPF/gtfLp08r/4/)
- [MicroTaskQueue with click event handler](https://jsfiddle.net/MR_RPF/v5s4dofr/32/)
- [setTimeout vs requestAnimationFrame (with CSS Transitions)](https://codepen.io/ahomu/pen/NRjxrG)

## 관련 자료
- [Asynchronous JavaScript & EVENT LOOP from scratch 🔥 | Namaste JavaScript Ep.15](https://www.youtube.com/watch?v=8zKuNo4ay8E)
- [Jake Archibald: In The Loop - JSConf.Asia](https://www.youtube.com/watch?v=cCOL7MC4Pl0)
- [Jake Archibald: Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
- [Jake Archibald: Javascript microtask quiz](https://www.youtube.com/watch?v=Lum0R6Ng6R8)
- [html.spec.whatwg - event loop processing model](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)
- [google - inside browser part series](https://developer.chrome.com/blog/inside-browser-part1/)
- [Do events bubble in microtasks?](https://stackoverflow.com/questions/70734518/do-events-bubble-in-microtasks)
- [Why is there a distinction between microtask and (macro)task in JavaScript?](https://stackoverflow.com/questions/68525922/why-is-there-a-distinction-between-microtask-and-macrotask-in-javascript/68598260#68598260)
- [Using microtasks in JavaScript with queueMicrotask()](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)
- [setTimeout vs requestAnimationFrame](https://stackoverflow.com/questions/71523029/settimeout-vs-requestanimationframe/71523642#71523642)
- [DOMContentLoaded event and task queue](https://stackoverflow.com/questions/71318123/domcontentloaded-event-and-task-queue)  
- [Event dispatch and DOM event flow](https://w3c.github.io/uievents/#event-flow)
- [In depth analysis of event loop and browser rendering, frame animation, idle callback](https://developpaper.com/in-depth-analysis-of-event-loop-and-browser-rendering-frame-animation-idle-callback-animation-demonstration/)
- [Rendering Processing Model](https://docs.google.com/document/d/1Mw6qNw8UAEfW96CXaXRVYPPZjqQS3YdK7v57wFttAhs/edit#heading=h.p82ypq23qxwh)

## 질문
### Task란 무엇인가요?
[공식 문서](https://html.spec.whatwg.org/multipage/webappapis.html#concept-task)에서 확인 가능합니다.  

### 정말 queue는 3개 뿐인가요?
[비슷한 이벤트 끼리 따로 따로 다른 queue에 넣습니다](https://html.spec.whatwg.org/multipage/webappapis.html#task-source). 여러개 있고 브라우저가 우선순위에 맞게 queue에서 task를 가져와 처리합니다.  

### AnimationFrameQueue를 처리하다가 도중에 microTask가 MicroTaskQueue에 들어가면, 어떻게 그걸 먼저 실행하나요?
[run the animation frame callbaks](https://html.spec.whatwg.org/multipage/imagebitmap-and-animations.html#run-the-animation-frame-callbacks)단계에서 handler를 [invoke](https://webidl.spec.whatwg.org/#invoke-a-callback-function)하는데, 14번째 step에서 [clean up after running script](https://html.spec.whatwg.org/multipage/webappapis.html#clean-up-after-running-script)단계에 들어가는데, 이때 3번째 항목에 execution context stack이 비어있을때 [perform a microtask checkpoint](https://html.spec.whatwg.org/multipage/webappapis.html#perform-a-microtask-checkpoint)을 한다. 다시말해서, AnimationFrameQueue에 들어있는 callback을 실행하고 execution context stack이 비게되면, micro task를 실행한다는 말이다.  

### click event handler는 AnimationFrameQueue에 들어가나요?
공식 문서에 나와있지는 않지만, [마치 그런것처럼 작동한다는 의견](https://stackoverflow.com/questions/71568745/animation-frame-queue-vs-micro-task-queue#comment126513157_71568840)이 있습니다.  

### addEventListener로 등록한 콜백 함수는 어디에 저장되나요?  
- [공식 문서](https://dom.spec.whatwg.org/#eventtarget-event-listener-list)를 보면 EventTarget에 event listener list가 있다고 나온다. 여기에 담깁니다.
- [공식 문서](https://html.spec.whatwg.org/multipage/webappapis.html#event-handler-attributes)를 보면, EventTarget에 event handler들이 들어있는 event handler map이 있다고 나옵니다. 
- 결론적으로 EventTarget에 담깁니다.

### 이벤트가 dispatch된후 처리되는 과정
event target을 찾고, [propagation path](https://w3c.github.io/uievents/#propagation-path)가 결정된후, capture phase, target phase, bubble phase를 거치며 이벤트 객체와 함께 event listener가 호출된다.
[참고문서](https://w3c.github.io/uievents/#event-flow)
