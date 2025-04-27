# ComoCamp Website

This repository contains the source code for the **ComoCamp** website, built using the [Hugo](https://gohugo.io/) static site generator. The website serves as the central hub for information about the Collaborative Modeling Camp, including event details, participation, sponsors, and more.

## Project Structure

- **`archetypes/`**: Default content templates for Hugo.
- **`content/`**: Markdown files for the website's content, organized by sections such as `community`, `camp`, `participation`, etc.
- **`layouts/`**: Custom HTML templates for rendering the website, including partials like `header.html` and `footer.html`.
- **`static/`**: Static assets like images, CSS, and JavaScript files.
- **`resources/`**: Generated resources, such as resized images.
- **`config.toml`**: Hugo configuration file for site settings.
- **`.editorconfig`**: Editor configuration for consistent coding styles.
- **`.gitignore`**: Specifies files and directories to ignore in version control.

## Key Features

- **Dynamic Sections**: The website is modular, with sections like "Community," "Sponsors," and "FAQs" dynamically rendered based on Hugo's configuration.
- **Responsive Design**: Built with Bootstrap for mobile-friendly layouts.
- **Custom Styling**: Includes custom CSS for branding and design consistency.
- **SEO-Friendly**: Automatically generates `robots.txt` and supports metadata for social sharing.

## Prerequisites

- [Hugo](https://gohugo.io/) (version 0.142.0, not yet compatible with 0.1.46.0 or higher)
- Node.js (optional, for managing additional dependencies)

## Getting Started

1. Clone the repository:
    ```bash
    git clone https://github.com/your-repo/comocamp.git
    cd comocamp
    ```

2. Install Hugo:
    Follow the [Hugo installation guide](https://gohugo.io/getting-started/installing/).

3. Run the development server:
    ```bash
    hugo server

    # command to rebuild all, open browser and navigate to changed
    # https://gohugo.io/commands/hugo_server/
    hugo server -D --cleanDestinationDir --printPathWarnings --disableFastRender -O --navigateToChanged
    ```

4. Open your browser and navigate to `http://localhost:1313`.

## Deployment

The website is configured for deployment to `https://comocamp.org/`. To build the site for production, run:

```bash
hugo
```

The generated files will be located in the `public/` directory.

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bugfix.
3. Submit a pull request with a detailed description of your changes.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgments

- **[BootstrapMade](https://bootstrapmade.com/)**: For the design template.
- **[Hugo](https://gohugo.io/)**: For the static site generator.

For questions or feedback, contact us at [hello@comocamp.org](mailto:hello@comocamp.org).  