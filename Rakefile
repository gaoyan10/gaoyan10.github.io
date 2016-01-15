task :default => [:preview]

task :preview do
	sh "jekyll serve -w"
end

desc "generate ppt with pandoc"
task :generateppt, :file_name do |t, args|
  sh "cd keynote && pandoc -t revealjs -s #{args.file_name}.md -o #{args.file_name}.html --template=reveal.html -V revealjs-url='../public/reveal.js'"
end

desc "git and & commit"
task :commit, [:msg] do |t, args|
	msg = args.msg ? args.msg : 'update'
	if `git status | grep "nothing to commit"`.empty? 
		sh 'git add .'
		sh "git commit -m '#{msg}'"
	end
end

desc "add git commit and push to git remote master"
task :push => [:commit] do |t, args|
	unless `git status | grep "nothing to commit"`.empty? 
		sh "git push -u origin master"
	end
end
