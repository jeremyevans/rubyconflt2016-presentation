desc "Run slideshow server"
task :default do
  sh "showoff serve"
end

desc "Make static slideshow"
task :static do
  sh "rm -rf static"
  sh "showoff static"
end

desc "Make static slideshow"
task :tarball => [:static] do
  key = File.basename(File.dirname(__FILE__))
  sh "mv static #{key}"
  sh "tar zcf #{key}.tar.gz #{key}"
  sh "rm -r #{key}"
end
