# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.

require_relative 'config/application'

Rails.application.load_tasks

task copy_signature_files: :environment do
  require 'rbs_rails'

  to = Rails.root.join('sig/rbs_rails/')
  to.mkpath unless to.exist?
  RbsRails.copy_signatures(to: to)
end

task generate_rbs_for_model: :environment do
  require 'rbs_rails'

  out_dir = Rails.root / 'sig'
  out_dir.mkdir unless out_dir.exist?

  Rails.application.eager_load!

  reject_classes = [
    ActionText::RichText,
    ActionMailbox::InboundEmail,
    ActiveStorage::Blob,
    ActiveStorage::Attachment
  ]

  tables = ActiveRecord::Base.descendants - reject_classes
    
  tables.each do |klass|
    next if klass.abstract_class?

    path = out_dir / "app/models/#{klass.name.underscore}.rbs"
    FileUtils.mkdir_p(path.dirname)

    sig = RbsRails::ActiveRecord.class_to_rbs(klass)
    path.write sig
  end
end