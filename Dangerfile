# github comment settings
github.dismiss_out_of_range_messages

# Warn when there is a big PR
warn("Big PR, try to keep changes smaller if you can") if git.lines_of_code > 500

# Detekt 1.0.0.RC5-6 include root project folder in file name
checkstyle_format.base_path = File.basename(Dir.getwd)
checkstyle_format.report 'app/build/reports/detekt/detekt-checkstyle.xml'
