ROOT    = File.dirname(__FILE__)
VERSION = "1.9.3-p194"

require "erb"
require "tmpdir"

def tempdir
  Dir.mktmpdir do |dir|
    Dir.chdir(dir) do
      yield(dir)
    end
  end
end

task :default => "pkg/ruby-#{VERSION}.pkg"

file "pkg/ruby-#{VERSION}.pkg" => "/usr/local/heroku/ruby" do |t|
  tempdir do |dir|
    cp_r t.prerequisites.first, "#{dir}/ruby"

    kbytes = %x{ du -ks ruby | cut -f 1 }
    num_files = %x{ find ruby | wc -l }

    mkdir_p "pkg"
    mkdir_p "pkg/Resources"
    mkdir_p "pkg/ruby-#{VERSION}.pkg"

    dist = File.read("#{ROOT}/support/Distribution.erb")
    dist = ERB.new(dist).result(binding)
    File.open("pkg/Distribution", "w") { |f| f.puts dist }

    dist = File.read("#{ROOT}/support/PackageInfo.erb")
    dist = ERB.new(dist).result(binding)
    File.open("pkg/ruby-#{VERSION}.pkg/PackageInfo", "w") { |f| f.puts dist }

    sh %{ mkbom -s ruby pkg/ruby-#{VERSION}.pkg/Bom }

    Dir.chdir("ruby") do
      sh %{ pax -wz -x cpio . > ../pkg/ruby-#{VERSION}.pkg/Payload }
    end

    mkdir_p "#{ROOT}/pkg"
    sh %{ pkgutil --flatten pkg #{ROOT}/#{t.name} }
  end
end

file "/usr/local/heroku/ruby" do |t|
  sh "vendor/ruby-build/bin/ruby-build #{VERSION} #{t.name}"
end
