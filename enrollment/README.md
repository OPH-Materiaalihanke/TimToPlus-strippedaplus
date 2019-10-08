A+ enrollment questionnaire - template for common questions
===========================================================

This repository contains a template for an A+ enrollment questionnaire.
Enrollment questionnaire is a normal A+ questionnaire that students must
complete when they enroll in the course in A+.

How to use this in your course
------------------------------

1. Ensure that the course is using an up-to-date version of the a-plus-rst-tools
   (this template defines the questionnaire in RST markup).
   The version matching the git checksum `7e18045` (15th Aug 2019) is at least new enough.
2. Modify the `conf.py` file in the root of your course repository.
   Add these definitions for the `course_key` and `rst_prolog` variables as well as
   `exclude_patterns`:

   ```python
   # The JavaScript code used by the enrollment questionnaire is hosted in the course repo,
   # so we need to know the course key in order to craft the URL of the JS.
   # This RST substitution is used to insert the script in the questionnaire RST code.
   course_key = os.environ.get('COURSE_KEY', 'default')
   rst_prolog = '''.. |enroll-js-script| raw:: html

     <script src="/static/{course_key}/_static/enrollmentquiz.js"></script>
   '''.format(course_key=course_key)
   
   # Add 'enrollment' to the exclude_patterns list. The list has probably
   # initially been defined with some elements.
   exclude_patterns = ['_build', '_data', 'enrollment']
   ```
   
   The environment variable `COURSE_KEY` should be defined in `build.sh` for the
   production mooc-grader and in `docker-compile.sh` for local testing.
   In `build.sh`, you need to know the course key used in the production installation
   and export the variable like this: `export COURSE_KEY='mycoursekey'`.
   In `docker-compile.sh`, add the parameter `-e "COURSE_KEY=default"` to the
   `docker run` command (the course key is `default` in the local testing container).
   
   If the `exclude_patterns` variable is not defined correctly, compiling the
   course RST fails with an error (because the question templates should not
   be compiled independently; they are included into another RST file):
   
   ```
   File "/path/to/course/a-plus-rst-tools/directives/questionnaire.py", line 152, in create_question
       env.question_count += 1
   AttributeError: 'BuildEnvironment' object has no attribute 'question_count'
   ```

3. Add this aplus-enrollment-questionnaire repository as a git submodule to
   the root of the course repository. The name of the directory inside the
   course repository shall be `enrollment`. The submodule contains commonly
   used questions and files.

   ```
   git submodule add https://github.com/Aalto-Letech/aplus-enrollment-questionnaire.git enrollment
   ```

   If the git submodule has been added to your course git repository and you
   do not have it yet in your local repository in your computer, you need to run
   the command `git pull && git submodule update --init`.

4. You need to add the enrollment questionnaire to the course contents as follows.
   
   1. Modify the `index.rst` file of the first exercise round (module).
   (Or a different module if you know what you are doing.)
   The file may have a different name since it is defined in the main `index.rst`
   of the course. The module index.rst defines the chapters of the module with
   the `toctree` directive. **Add** either the chapter `enrollment_en` or `enrollment_fi`
   (English or Finnish version) to the **end** of the first module in
   the module index.rst. **Note**: the opening and closing times of the module
   affect the enrollment questionnaire too.
   2. **Copy** the corresponding file (`enrollment_en.rst` or `enrollment_fi.rst`)
   from the `enrollment` directory into the module directory.
   3. You may modify the questionnaire in that file if necessary.
      If the course is not offered to external students (who use Google login),
      then you may remove the questionnaire `enrollexternalexercise` from the RST file.

5. Create a symbolic link pointing to the file `enrollment/_static/enrollmentquiz.js`
   in the path `_static/enrollmentquiz.js` so that the JS file is included
   in the static files of the course. (If there are issues with symbolic links,
   just copy the file.)

   ```bash
   cd _static
   ln -s ../enrollment/_static/enrollmentquiz.js enrollmentquiz.js
   ```

