desc "Starts the blog on port 4000"
task :start do
  exec "jekyll server -w"
end

desc "Deploy the website to gavinjoyce.com"
task :deploy do
  exec "s3_website push"
end
