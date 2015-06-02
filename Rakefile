require 'rake/clean'
require 'rplusplus'
env = RPlusPlus::Environment.new

CC = 'g++'

LIBS = ['webkit2gtk-3.0']

LIB_CFLAGS = LIBS.map { |lib|
                %x[ pkg-config --cflags #{lib} ].gsub(/\n/,' ')
             }.flatten.join(' ')
LDLIBS = LIBS.map {
           |lib| %x[ pkg-config --static --libs #{lib} ].gsub(/\n/,' ')
         }.flatten.join(' ')

WARNING_FLAGS = [
  'all', 'extra', 'effc++', 'init-self', 'missing-include-dirs', 'switch-default', 'switch-enum', 'unused-parameter',
  'strict-overflow=5', 'float-equal', 'shadow', 'c++0x-compat', 'conversion', 'sign-conversion', 'missing-declarations',
  'overloaded-virtual', 'sign-promo'
].map { |flag| "-W#{flag}"}.join ' '
FORMATTING_FLAGS = ['message-length=80', 'diagnostics-show-option'].map { |flag| "-f#{flag}"}.join ' '
EXTRA_CFLAGS = "-std=c++11 -pipe -pedantic"
CFLAGS = "#{WARNING_FLAGS} #{FORMATTING_FLAGS} #{LIB_CFLAGS} #{EXTRA_CFLAGS}"

EXTRA_LDFLAGS = ""
LDFLAGS = "#{LDLIBS} #{EXTRA_LDFLAGS}"

CLOBBER.include(*env.objects.keys, *env.builds.keys, *env.erbs.keys)

task :default => :all

task :all => ['pidash']

# compile ERBs into cpp/h files
env.erbs.each do |target, sources|
  file target => sources do |t|
    File.write(target, ERB.new(File.read(sources.first)).result)
  end
end

# compile CPPs into O-files
env.objects.each do |object, sources|
  file object => sources do |t|
    sh "#{CC} #{CFLAGS} -c #{sources.first} -o #{object}"
  end
end

# build executables from main CPPs
env.builds.each do |target, objects|
  file target => objects do |t|
    sh "#{CC} #{objects.join(' ')} -o #{target} #{LDFLAGS}"
  end
end
