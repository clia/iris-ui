Pure Rust UI framework

Philosophy: The UI is a projection of a virtual world onto the screen.

## Example: A lovely girl

```rust
use iris_ui::prelude::*;

struct LovelyGirl {
    girl: Girl,
}

impl Default for LovelyGirl {
    fn default() -> Self {
        Self {
            girl: Girl {
                hair: Black,
                skin: Yellow,
                body: Slim,
                look: Beautiful,
                every_morning: [
                    SayHi,
                    PrepareBreakfast,
                ]
                .into(),
            },
        }
    }
}

fn world() -> World {
    World {
        root: LovelyGirl::default(),
    }
}

fn main() {
    iris_ui::launch(world);
}
```

## Example: inner text clock

```rust
//! A simple little clock that updates the time every few milliseconds.

use iris_ui::prelude::*;

fn main() {
    iris_ui::launch(world);
}

fn world() -> World {
    World::new()
      .content([
        Board::new()
          .width(VIEWPORT.width)
          .height(VIEWPORT.height)
          .horizontal_align(HorizontalAlign::Center)
          .vertical_align(VerticalAlign::Middle)
          .children([
            Card::new()
              .children([
                Row::new()
                  .children([
                    Text::from_str("Carpe diem 🎉"),
                  ]),
                Row::new()
                  .children([
                    TextTimer::new()
                      .format("%H:%M:%S"),
                  ]),
              ]),
          ]),
      ])
}
```

## Example: text clock updated by outer world

```rust
//! A simple little clock that updates the time every few milliseconds.

use async_std::task::sleep;
use iris_ui::prelude::*;
use web_time::Instant;

fn main() {
    iris_ui::launch(world);
}

fn world() -> World {
    let mut millis = use_signal(|| 0);

    use_future(move || async move {
        // Save our initial timea
        let start = Instant::now();

        loop {
            sleep(std::time::Duration::from_millis(27)).await;

            // Update the time, using a more precise approach of getting the duration since we started the timer
            millis.set(start.elapsed().as_millis() as i64);
        }
    });

    // Format the time as a string
    // This is rather cheap so it's fine to leave it in the render function
    let time = format!(
        "{:02}:{:02}:{:03}",
        millis() / 1000 / 60 % 60,
        millis() / 1000 % 60,
        millis() % 1000
    );

    World::new()
      .content([
        Board::new()
          .width(VIEWPORT.width)
          .height(VIEWPORT.height)
          .horizontal_align(HorizontalAlign::Center)
          .vertical_align(VerticalAlign::Middle)
          .children([
            Card::new()
              .children([
                Row::new()
                  .children([
                    Text::from_str("Carpe diem 🎉"),
                  ]),
                Row::new()
                  .children([
                    Text::from_str("{time}"),
                  ]),
              ]),
          ]),
      ])
}
```
