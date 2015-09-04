ASSEMBLY_INFO = 'src/SimpleMigrations/Properties/AssemblyInfo.cs'
NUSPEC = 'NuGet/SimpleMigrations.nuspec'
CSPROJ = 'src/SimpleMigrations/SimpleMigrations.csproj'
MSBUILD = %q{C:\Program Files (x86)\MSBuild\12.0\Bin\MSBuild.exe}

GITLINK_REMOTE = 'https://github.com/canton7/SimpleMigrations'

desc "Create NuGet package"
task :package do
  local_hash = `git rev-parse HEAD`.chomp
  sh "NuGet/GitLink.exe . -s #{local_hash} -u #{GITLINK_REMOTE} -f SimpleMigrations.sln"
  Dir.chdir(File.dirname(NUSPEC)) do
    sh "nuget.exe pack #{File.basename(NUSPEC)}"
  end
end

desc "Bump version number"
task :version, [:version] do |t, args|
  parts = args[:version].split('.')
  parts << '0' if parts.length == 3
  version = parts.join('.')

  content = IO.read(ASSEMBLY_INFO)
  content[/^\[assembly: AssemblyVersion\(\"(.+?)\"\)\]/, 1] = version
  content[/^\[assembly: AssemblyFileVersion\(\"(.+?)\"\)\]/, 1] = version
  File.open(ASSEMBLY_INFO, 'w'){ |f| f.write(content) }

  content = IO.read(NUSPEC)
  content[/<version>(.+?)<\/version>/, 1] = args[:version]
  File.open(NUSPEC, 'w'){ |f| f.write(content) }
end

desc "Build the project for release"
task :build do
  sh MSBUILD, CSPROJ, "/t:Clean;Rebuild", "/p:Configuration=Release", "/verbosity:normal"
end