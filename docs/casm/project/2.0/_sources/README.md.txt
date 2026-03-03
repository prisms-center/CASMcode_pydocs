# Building notebooks:

    jupyter nbconvert --to html --template casmnb --TemplateExporter.extra_template_basedirs=. notebooks/**/*.ipynb
