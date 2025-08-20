# iris-ui
Pure Rust UI framework

## Example

```rust
//! A simple little clock that updates the time every few milliseconds.

use async_std::task::sleep;
use iris_ui::prelude::*;
use web_time::Instant;

fn main() {
    iris_ui::launch(app);
}

fn app() -> App {
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

    App::new()
      .children([
        Page::new()
          .fill_mode(FillMode::Full)
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
