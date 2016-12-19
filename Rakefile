# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

require 'rubygems'
require 'echoe'

VERSION = File.open('VERSION').read.chomp

Echoe.new('avro', VERSION) do |p|
  p.author = "Apache Software Foundation"
  p.email = "dev@avro.apache.org"
  p.summary = "Apache Avro for Ruby"
  p.description = "Avro is a data serialization and RPC format"
  p.url = "http://avro.apache.org/"
  p.runtime_dependencies = %w[multi_json]
  p.licenses = ["Apache License 2.0 (Apache-2.0)"]
end

task :test => [:interop]

t = Rake::TestTask.new(:interop)
t.pattern = 'interop/test*.rb'

task :generate_interop do
  $:.unshift(HERE + '/lib')
  $:.unshift(HERE + '/test')
  require 'avro'
  require 'random_data'

  mkdir_p HERE + "/interop/data"

  schema = Avro::Schema.parse(File.read(SCHEMAS + '/interop.avsc'))
  r = RandomData.new(schema, ENV['SEED'])
  f = File.open(HERE + '/interop/data/ruby.avro', 'w')
  writer = Avro::DataFile::Writer.new(f, Avro::IO::DatumWriter.new(schema), schema)
  begin
    writer << r.next
    writer << r.next
  ensure
    writer.close
  end

  Avro::DataFile.open(HERE + '/interop/data/ruby-deflate.avro', 'w', schema.to_s, :deflate) do |writer|
    20.times { writer << r.next }
  end
end


HERE = File.expand_path(File.dirname(__FILE__))
SHARE = HERE + '/share'
SCHEMAS = SHARE + '/test/schemas'

task :dist => [:gem] do
  mkdir_p "dist/"
  cp "pkg/avro-#{VERSION}.gem", "dist/"
end
