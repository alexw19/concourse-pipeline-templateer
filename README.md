## WAT

This tool enables you to split your `pipeline.yml` into several single files, as many as you like and generates a `fly`
compatible `pipeline.yml` while merging `jobs`/`resource_types`/`resources`/`groups`

## Installation

```console
gem install ctpl
```

## Usage

1. Create a folder `pipeline` in your e.g. `ci` folder `ci/pipeline`
2. Move your `ci/pipeline.yaml` to `ci/pipeline/pipeline.yaml`

Now run the generation

```console
cd ci
ctpl
# above assumed that your base folder is ./pipeline and the file should be generated to ./pipeline.yaml 
# that's just the default - more to come
```

That's about it for the first step - now is the best time to run and read

```console
ctpl help
```

Now go on and split your `pipeline.yaml` into for example

- `ci/pipeline/resources/_resources_git.yml`
- `ci/pipeline/resources/_resources_docker.yml`
- `ci/pipeline/jobs/_staging.yml`
- `ci/pipeline/jobs/_candidate.yml`
- `ci/pipeline/jobs/_release.yml`

This is just an example, you could not use folders at all or even deeper nesting. The only rule is, all "partials"
so all files to be merged into your main `ci/pipeline/pipeline.yaml` must begin match this glob `_*.yaml`

I kept the actual "format" fairly open, so you can work 
 - with folder or without folders
 - you can group your partials by `jobs` (so jobs and their resources)
 - you could also rather group by type, so group `jobs`, `resources` into several files
 - you can create a file per job/resource each or combine them
 
Or you can just mix all those. 

### Advanced 

#### Options

List all options using `ctpl help` but in general you have a usecase like

This assumes your `pipeline.yaml` is at `./pipeline_special/pipeline.yaml` and generates the result to `/tmp/pipeline.yaml`

```console
ctpl -b ./pipeline_special -o /tmp/pipeline.yaml
```

#### Global Aliases

You might want to define global aliases which should be available for all your partials, e.g. for notifications, failures
or whatever suits you. For this, there is a special file `<basefolder>/aliases.yaml` so with the example here
`ci/pipeline/aliases.yaml` - now you can put things into that like

```yaml
failure_params: &failure_params
  params:
    color:                red
    notify:               true
    from:                 "Concourse CI"
    message:
      template:           'Failed'
    message_format:       html
```

And now use this in your partials as it would have been defined in the partial itself. Reuse - reuse - reuse.

#### Local aliases

You can of course have aliases which you define in the partials locally, but please be aware that those should not conflict
with any other alias or they could colide. If you need an alias in more then one partial, put it under `aliases.yaml`

## FAQ

**Is the `<basefolder>/pipeline.yaml` file required**
No

**Is the `<basefolder>/aliases.yaml` file required**
No 

**Is can you use custom keys in your partials**
Yes - any. We only merge `jobs`/`resource_types`/`resources`/`groups` - anything else is just combined as yaml would do it
This also means, that if you have several things defining a toplevel `foo: bar` - the one included as the last one wins

**Can i define a order**
The glob is sorted, for now all you can do is using things like
 
- `ci/pipeline/_1_staging.yml`
- `ci/pipeline/_2_candidate.yml`
- `ci/pipeline/_3_release.yml`

This would put the staging jobs/resources first, then candidate then releases.

## Contribute / Features

Yes please, i beg you :) - But please do not open FR in issues when you do not want to work on them yourself - only for pre-discussions.
