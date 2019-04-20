---
id: events
title: SyntheticEvent
permalink: docs/events.html
layout: docs
category: Reference
---

מדריך עזר זה מסביר על עטיפת ה- `SyntheticEvent` המהווה חלק ממערכת האירועים של ריאקט. ראה [טיפול באירועים](/docs/handling-events.html) על מנת ללמוד עוד.

## סקירה כללית {#overview}

מטפלי האירועים שלך מקבלים מופעים של `SyntheticEvent`, שהוא מעטפת בין דפדפנים מסביב לאירוע המקורי של הדפדפן. יש לו את אותו ממשק כמו האירוע המקורי, כולל `stopPropagation()` ו- `preventDefault()`, למעט זה שהאירועים עובדים באופן זהה בין כל הדפדפנים.

אם אתה מגלה שאתה זקוק לאירוע הדפדפן הבסיסי מסיבה כלשהי, השתמש בתכונה `nativeEvent` על מנת לקבל אותו. כל אובייקט `SyntheticEvent` מכיל את התכונות הבאות:

```javascript
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
DOMEventTarget target
number timeStamp
string type
```

> הערה:
>
> החל מגרסה 0.14, החזרת `false` ממטפל אירועים כבר לא תעצור התפשטות אירועים. לחלופין, `e.stopPropagation()` או `e.preventDefault()` יופעלו ידנית, בהתאם לצורך.

### איגוד אירועים {#event-pooling}

`SyntheticEvent` הוא מאוגד. זה אומר שייעשה שימוש חוזר באובייקט ה- `SyntheticEvent` וכל מאפייניו יבוטלו לאחר שה- callback של האירוע יופעל. דבר זה קורה בגלל סיבות שקשורות לביצועים. כתוצאה מכך, לא ניתן לגשת לאירוע בצורה אסינכרונית. 


```javascript
function onClick(event) {
  console.log(event); // => אובייקט מבוטל.
  console.log(event.type); // => "click"
  const eventType = event.type; // => "click"

  setTimeout(function() {
    console.log(event.type); // => null
    console.log(eventType); // => "click"
  }, 0);

  // לא יעבוד. this.state.clickEvent יכיל ערכי null.
  this.setState({clickEvent: event});

  // עדיין ניתן לייצא את מאפייני האירוע.
  this.setState({eventType: event.type});
}
```

> הערה:
>
> אם אתה רוצה לגשת למאפייני האירוע בצורה אסינכרונית, ניתן לעשות זאת באמצעות קריאה ל- `event.persist()` באירוע, דבר שיסיר את האירוע הסינתטי מהאיגוד ויאפשר הפניות לאירוע להישמר על ידי קוד המשתמש.

## אירועים נתמכים {#supported-events}

ריאקט מנרמל אירועים כך שיש להם מאפיינים קבועים בין דפדפנים שונים.

מטפלי האירועים שלהלן מופעלים על ידי אירוע בשלב הבעבוע. על מנת לרשום מטפל אירועים לשלב הלכידה, הוסף `Capture` לשם האירוע; לדוגמה, במקום שימוש ב- `onClick`, נשתמש ב- `onClickCapture` על מנת לטפל באירוע ההקלקה בשלב הלכידה.

- [אירועי clipboard](#clipboard-events)
- [אירועי קומפוזיציה](#composition-events)
- [אירועי מקלדת](#keyboard-events)
- [אירועי פוקוס](#focus-events)
- [אירועי טפסים](#form-events)
- [אירועי עכבר](#mouse-events)
- [אירועי מצביע](#pointer-events)
- [אירועי בחירה](#selection-events)
- [אירועי טאץ'](#touch-events)
- [אירועי ממשק משתמש](#ui-events)
- [אירועי גלגל](#wheel-events)
- [אירועי מדיה](#media-events)
- [אירועי תמונה](#image-events)
- [אירועי אנימציה](#animation-events)
- [אירועי מעבר](#transition-events)
- [אירועים אחרים](#other-events)

* * *

## עיון {#reference}

### אירועי clipboard {#clipboard-events}

שמות אירועים:

```
onCopy onCut onPaste
```

מאפיינים:

```javascript
DOMDataTransfer clipboardData
```

* * *

### אירועי קומפוזיציה {#composition-events}

שמות אירועים:

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

מאפיינים:

```javascript
string data

```

* * *

### אירועי מקלדת {#keyboard-events}

שמות אירועים:

```
onKeyDown onKeyPress onKeyUp
```

מאפיינים:

```javascript
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

מאפיין ה- `key` יכול לקבל כל ערך שנמצא ב- [אירועי DOM שלב שלישי](https://www.w3.org/TR/uievents-key/#named-key-attribute-values).

* * *

### אירועי פוקוס {#focus-events}

שמות אירועים:

```
onFocus onBlur
```

אירועי הפוקוס הללו עובדים על כל האלמנטים ב- DOM של ריאקט, לא רק אלמנטי טפסים.

מאפיינים:

```javascript
DOMEventTarget relatedTarget
```

* * *

### אירועי טפסים {#form-events}

שמות אירועים:

```
onChange onInput onInvalid onSubmit
```

לקבלת מידע נוסף על אירוע ה- onChange, ראה [טפסים](/docs/forms.html).

* * *

### אירועי עכבר {#mouse-events}

שמות אירועים:

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

אירועי ה- `onMouseEnter` ו- `onMouseLeave` מופצים מהאלמנט השמאלי אל האלמנט שהוזן במקום בעבוע רגיל ואין לו שלב לכידה.

מאפיינים:

```javascript
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
```

* * *

### אירועי מצביע {#pointer-events}

שמות אירועים:

```
onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture
onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut
```

אירועי ה- `onPointerEnter` ו- `onPointerLeave` מופצים מהאלמנט השמאלי אל האלמנט שהוזן במקום בעבוע רגיל ואין לו שלב לכידה.

מאפיינים:

כמו שהוגדר ב- [W3 spec](https://www.w3.org/TR/pointerevents/), אירועי מצביע מרחיבים [אירועי עכבר](#mouse-events) עם המאפיינים הבאים:

```javascript
number pointerId
number width
number height
number pressure
number tangentialPressure
number tiltX
number tiltY
number twist
string pointerType
boolean isPrimary
```

הערה לגבי תמיכה בדפדנים:

אירועי מצביע עדיין לא נתמכים בכל דפדפן( בזמן כתיבת מאמר זה, הדפדנים הנתמכים הם: כרום, פיירפוקס, edge, ו- internet explorer). ריאקט באופן מכוון לא תומכת בדפדפנים אחרים כי 'פוליפיל' רגיל יגדיל משמעותית את הגודל של `react-dom`.

אם היישום שלך דורש אירועי מצביע, אנו ממליצים שתוסיף 'פוליפיל' צד שלישי.

* * *

### אירועי בחירה {#selection-events}

שמות אירועים:

```
onSelect
```

* * *

### אירועי טאץ' {#touch-events}

שמות אירועים:

```
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

מאפיינים:

```javascript
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

* * *

### אירועי ממשק משתמש {#ui-events}

שמות אירועים:

```
onScroll
```

מאפיינים:

```javascript
number detail
DOMAbstractView view
```

* * *

### אירועי גלגל {#wheel-events}

שמות אירועים:

```
onWheel
```

מאפיינים:

```javascript
number deltaMode
number deltaX
number deltaY
number deltaZ
```

* * *

### אירועי מדיה {#media-events}

שמות אירועים:

```
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted
onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay
onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend
onTimeUpdate onVolumeChange onWaiting
```

* * *

### אירועי תמונה {#image-events}

שמות אירועים:

```
onLoad onError
```

* * *

### אירועי אנימציה {#animation-events}

שמות אירועים:

```
onAnimationStart onAnimationEnd onAnimationIteration
```

מאפיינים:

```javascript
string animationName
string pseudoElement
float elapsedTime
```

* * *

### אירועי מעבר {#transition-events}

שמות אירועים:

```
onTransitionEnd
```

מאפיינים:

```javascript
string propertyName
string pseudoElement
float elapsedTime
```

* * *

### אירועים אחרים {#other-events}

שמות אירועים:

```
onToggle
```
