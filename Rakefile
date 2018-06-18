require 'tempfile'

issues_folder = 'issues'

desc "Update the issues list"
task :update do
  sh "git submodule foreach git pull"
end

def stripped_front_matter_temp_file(issue)
  f = Tempfile.new('title')
  begin
    found_start = false
    found_end = false
    issue.each_line do |line|
      line = line.strip
      if not found_start
        found_start = line == '---'
      elsif not found_end
        found_end = line == '---'
      else
        f.puts line
      end
    end
  ensure
    f.close
  end
  return f
end

def title_temp_file(issue)
  f = Tempfile.open('title')
  begin
    issue_number = File.basename(issue, '.*').split('-').last.to_i
    f.puts "# The iOS Times - #{issue_number}"
    f.puts "### #{File.basename(issue)[0, 10]}"
  ensure
    f.close
  end
  return f
end

desc "Generate the HTML code for the latest issue, and copy it to the clipboar"
task :build => [:update] do
  latest = Dir["#{issues_folder}/*.md"].reject { |f| File.basename(f) == "README.md" }.sort.last
  puts latest
  issue = File.open(latest)
  temp_issue = stripped_front_matter_temp_file(issue)
  temp_title = title_temp_file(issue)
  sh "redcarpet --parse-fenced_code_blocks --smarty #{temp_title.path} header.md #{temp_issue.path} footer.md | pbcopy"
  puts "👍  HTML code for #{latest} copied to clipboard"
  temp_issue.unlink
  temp_title.unlink
  issue.close
end
