Protothreads
=================================================================

This is a fork of Adam Dunkels' [protothreads](http://dunkels.com/adam/pt/) library,
ready to be used as a library in Arduino projects.

To use it e.g. in the [Arduino Online Editor](https://create.arduino.cc/) you can
download the Zip-File from the **dist** folder and reimport it in the IDE.

Below is a basic example arduino sketch that shows how the library can be used.

```C++
// import library
#include <pt.h>

// declare threads
static struct pt pt1, pt2;
static unsigned long globalMillis = 0;

void setup() {

  // initialize threads
  PT_INIT(&pt1);
  PT_INIT(&pt2);
}

void loop() {

  // get count of milliseconds since last passing of loop
  unsigned long ts = millis();
  unsigned long ticks = ts - globalMillis;
  if (ticks > 60000)
    ticks = 60000;
  globalMillis = ts;

  // call threads
  thread_1(&pt1, ticks);
  thread_2(&pt2, ticks);
}

int thread_1(struct pt *pt, uint16_t ticks)
{
  // increment timer
  static uint16_t timer;
  timer += ticks;

  // protothread start
  PT_BEGIN(pt);

  while (1)
  {
    // ...
    // do something
    // ...
    
    PT_WAIT_UNTIL(pt, timer > 5000); // wait 5 seconds (switch to thread 2)
    
    // ...
    // do something else
    // ...
  }

  // protothread end
  PT_END(pt);
}

int thread_2(struct pt *pt, uint16_t ticks)
{
  static uint16_t timer;

  timer += ticks;

  PT_BEGIN(pt);

  while (1)
  {
    // ...
    // do something
    // ...
    
    PT_WAIT_UNTIL(pt, timer > 5000); // wait 5 seconds (switch to thread 1)
    
    // ...
    // do something else
    // ...
  }

  PT_END(pt);
}
```
