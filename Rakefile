# -*- coding: utf-8 -*-
BASE = File.dirname(__FILE__)

SLIDES_DIR = File.join(BASE, 'slides')
HTML_DIR   = File.join(BASE, 'html')
CODE_DIR = File.join(BASE, 'code')
UI_DIR = File.join(BASE, 'ui/default')

ALL_HTML   = File.join(HTML_DIR, "all.html")

METADATA = File.join(SLIDES_DIR, "metadata.yml")

Dir.chdir(SLIDES_DIR) { SRC = FileList['*.slides']; SRC.resolve }

OUTPUT = []

SRC.each do |file_name|
  slide_file = File.join(SLIDES_DIR, file_name)
  html_file = File.join(HTML_DIR, file_name.ext('.html'))
  OUTPUT << html_file
  desc "Build #{html_file} from #{slide_file}"
  file html_file => [HTML_DIR, slide_file] do
    sh "ruby bin/pressie.rb #{METADATA} #{slide_file} > #{html_file}"
  end
end

desc "Build the HTML slides from all the files slides/*.slides files"
task :default => OUTPUT

desc "Build all slides based on the contents of slides/table_of_contents.slides"
task :all => [ :update_ui, :build_code, 'tmp/', HTML_DIR, ALL_HTML, :remove_tmp ]

task ALL_HTML => 'tmp/almost_all.html' do
  sh "ruby bin/postprocess_all.rb tmp/almost_all.html >#{ALL_HTML}"
end

task 'tmp/almost_all.html' => 'tmp/almost_all.slides' do
  sh "ruby bin/pressie.rb #{METADATA} tmp/almost_all.slides >tmp/almost_all.html"
end

task 'tmp/almost_all.slides' => OUTPUT do
  sh "ruby bin/build_all.rb #{METADATA} slides/table_of_contents.slides tmp/almost_all.slides"
end

file "tmp/" do
  mkdir "tmp"
end

file "html/" do
  mkdir "html"
end

task :update_ui do
  Dir.chdir(UI_DIR) do
    sh "sass pretty.sass pretty.css"
  end
end

task :build_code => [:build_haml, :build_sass]

task :build_haml do
  Dir.chdir(File.join(CODE_DIR, 'haml/')) do
    Dir.glob('*.haml').each do |file|
      basename = File.basename(file, '.haml')
      sh "haml #{file} #{basename}.html" unless basename =~ /^_/
    end
  end
end


task :build_sass do
  Dir.chdir(File.join(CODE_DIR, 'sass/')) do
    Dir.glob('*.sass').each do |file|
      basename = File.basename(file, '.sass')
      sh "sass --style expanded #{file} #{basename}.css" unless basename =~ /^_/
    end
  end
end

task :remove_tmp do
  FileUtils.rm_rf("tmp")
end

desc "Remove all work products—slides and temporary files"
task :clean => :remove_tmp do
  FileUtils.rm_rf HTML_DIR
end
