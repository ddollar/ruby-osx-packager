BUILD_RUBY = "1.9.3-p194"
ROOT       = File.dirname(__FILE__)

desc "make a ruby package"
task :package do
  sh "#{ROOT}/vendor/ruby-build/bin/ruby-build #{BUILD_RUBY} /usr/local/heroku/ruby"
end
