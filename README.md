# riff-ani

Generating the animated mouse cursor files (.ani) for Windows.

## Example

```rust
use riff_ani::ico::{IconDir, IconDirEntry, IconImage, ResourceType};
use riff_ani::{Ani, AniHeader};

fn main() {
    let frames = vec![
        "./images/1.png",
        "./images/2.png",
        "./images/3.png",
        "./images/4.png",
    ];

    let out = File::create("./output.ani")
        .unwrap_or_else(|_| panic!("cannot create file {}", dest.to_string_lossy()));

    let ani = Ani {
        header: AniHeader {
            num_frames: frames.len() as u32,
            num_steps: frames.len() as u32,
            width: 48,
            height: 48,
            frame_rate: 2,
        },
        frames: frames.iter().map(create_cur).collect(),
    };

    ani.encode(&out)
        .unwrap_or_else(|_| panic!("cannot write file {}", dest.to_string_lossy()));
}

fn create_cur(path: &str) -> IconDir {
    let mut icon_dir = IconDir::new(ResourceType::Cursor);

    let file = std::fs::File::open(path)
        .unwrap_or_else(|_| panic!("cannot open png {}", path));

    let mut image = IconImage::read_png(file)
        .unwrap_or_else(|_| panic!("cannot read png {}", path));

    image.set_cursor_hotspot(Some((8, 8)));

    let entry = IconDirEntry::encode_as_png(&image)
        .unwrap_or_else(|_| panic!("cannot encode png {}", path));

    icon_dir.add_entry(entry);
    icon_dir
}
```

See also the docs for crate [ico](https://docs.rs/ico).
