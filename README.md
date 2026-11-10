# YouTube Video Generator

Automate the publication of YouTube videos or shorts with this open-source script. This script generates video content based on a given topic and publishes it to your YouTube account.

## Dependencies

- [moviepy](https://zulko.github.io/moviepy/)
- [openai](https://platform.openai.com/docs/api-reference/summary)
- [gtts](https://pypi.org/project/gTTS/)
- [httplib2](https://pypi.org/project/httplib2/)
- [oauth2client](https://pypi.org/project/oauth2client/)
- [apiclient](https://pypi.org/project/apiclient/)

## Installation

1. Clone this repository:

    ```bash
    git clone https://github.com/danivs10/Automatic-Video-Generator-for-youtube
    cd your-repo
    ```

2. Install dependencies
3. Update the necessary files with your secret tokens.

## Usage

Execute the `main.py` file with the topic of the video as an argument:

```bash
python main.py "your-topic"
```

This will generate a video about the specified topic and publish it to your YouTube account.

## Cinematic 3D scroll experience

[`cinematic-experience/`](cinematic-experience/) contains *Dos Mundos*, a single-file, scroll-driven
3D film (Three.js + GSAP ScrollTrigger + WebGL) in 13 scenes built on the backgrounds in
`cinematic-experience/visual_assets_neutral/`. Serve the folder (`python3 -m http.server`) and open
`index.html`. See its [README](cinematic-experience/README.md) for details.

## Contributing

Contributions are welcome! If you want to contribute to this project, please follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Make your changes and commit them.
4. Push your changes to your fork.
5. Submit a pull request.

## Output

The generated video will be published to your YouTube account.

## Known Issues

- No known issues at the moment.

## Credits

Developed by Daniel Varela Sánchez.
