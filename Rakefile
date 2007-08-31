require "rake"
require 'rake/clean'
require 'rake/gempackagetask'
require "rake/testtask"

desc "clean, compile, test"
task :default => %w[clean compile test]

Rake::TestTask.new("test") do |t|
  t.pattern = "test/**/*_test.rb"
end

desc "Builds the extension"
if RUBY_PLATFORM =~ /java/
  task :compile => :compile_java
else
  task :compile => %W[ext/mixology/Makefile ext/mixology/mixology.#{Config::CONFIG['DLEXT']}]
end

file "ext/mixology/Makefile" => ["ext/mixology/extconf.rb"] do
  Dir.chdir("ext/mixology") do
    ruby "extconf.rb"
  end  
end

file "ext/mixology/mixology.#{Config::CONFIG['DLEXT']}" do
  Dir.chdir("ext/mixology") do
    sh "make"
  end
  cp "ext/mixology/mixology.#{Config::CONFIG['DLEXT']}", "lib"
end

CLEAN.include %w[ext/mixology/Makefile ext/mixology/mixology.bundle lib/mixology.bundle]
CLEAN.include %w[ext/mixology/MixableService.class ext/mixology/mixable.jar lib/mixology.jar]

Gem::manage_gems

specification = Gem::Specification.new do |s|
	s.name   = "mixology"
  s.summary = "Mixology enables objects to mixin and unmix modules."
	s.version = "0.1.0"
	s.author = "anonymous z, Pat Farley, Dan Manges"
	s.description = s.summary
  s.homepage = "http://mixology.rubyforge.org"
  s.rubyforge_project = "mixology"
  s.has_rdoc = false
  s.autorequire = "mixology"
  s.files = FileList['ext/**/*.{c,rb}', '{lib,test}/**/*.rb', '^[A-Z]+$', 'Rakefile'].to_a
  if RUBY_PLATFORM =~ /mswin/
    s.platform = Gem::Platform::WIN32
    s.files += ["lib/mixology.so"]
  elsif RUBY_PLATFORM =~ /java/
    s.platform = "jruby"
    s.files += ["lib/mixology.jar"]
  else
    s.platform = Gem::Platform::RUBY
    s.extensions = FileList["ext/**/extconf.rb"].to_a
  end
end
Rake::GemPackageTask.new(specification) do |package|
  package.need_zip = false
  package.need_tar = false
end

desc "Compiles the JRuby extension"
task :compile_java do
  Dir.chdir("ext/mixology") do
    sh %{javac -source 1.4 -target 1.4 -classpath $JRUBY_HOME/lib/jruby.jar MixologyService.java}
    sh %{jar cf mixology.jar MixologyService.class}
    cp "mixology.jar", "../../lib/mixology.jar"
  end
end
