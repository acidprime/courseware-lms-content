<link rel="stylesheet" href="/static/selfpaced/selfpaced.css" />

<script defer="[]" src="//code.jquery.com/jquery-1.11.2.js" />

<script defer="[]" src="https://try.puppet.com/js/selfpaced.js" />

<div id="lesson">

  <div id="instructions">

    <div class="instruction-header">
<i class="fa fa-graduation-cap" />
Lesson
</div>

    <div class="instruction-content">

      <p>In the last lesson, we assigned the value of a variable using the <code>hiera()</code>
function and that variable was passed into a profile called <code>profile::dns</code>.
There is actually a better pattern for doing this, you can use the <code>hiera()</code>
function inside the class definition as the default parameter.</p>

      <p>So when you define your <code>profile::dns</code> class it would look something like this:</p>
      <pre>
class profile::dns (
  $dns_server = hiera('dns_server')
) {
...
}  
</pre>

      <p>Since you wouldn&#8217;t need to specify the <code>dns_server</code> parameter when you declare
the class, you can now include the <code>profile::dns</code> class with this code:</p>
      <pre>
include profile::dns
</pre>

      <p>Assigning default parameters with hiera is a very common convention and was
recommended best practice in the past. You&#8217;ll will often see it in older forge modules
or code written by developers who have used Puppet since before version 3.</p>

      <p>There&#8217;s a problem with using hiera this way; As you write more code like this,
it becomes very easy to lose track of which key/value pair applies to what class.
The standard convention is to use the name of class as part of the Hiera key
like this:
<code>profile::dns::dns_server</code>.</p>

      <p>This convention isn&#8217;t just to make things easy to remember. Since Puppet 3,
there has been a feature called &#8220;Automatic Parameter Lookup&#8221; which means you
can leave out the Hiera call from the class definition. When the Puppet master
compiles a catalog for your node it will check in Hiera before falling back to
the defaults.</p>

      <p>In our example, let&#8217;s assume there&#8217;s a default value in <code>profile::params</code>:</p>

      <pre>
class profile::dns (
  $dns_server = $profile::params::dns_server
) {
...
}
</pre>

      <p>Now if there is a value in Hiera for <code>profile::dns::dns_server</code> the master will
use that, otherwise it will fall back to what&#8217;s in <code>params.pp</code> of the &#8220;profile&#8221;
module. You may be thinking that Hiera understands namespaces, so it&#8217;s somehow
filing the <code>dns_server</code> key under <code>profile::dns</code>, but that isn&#8217;t correct. Hiera
just uses the entire string <code>profile::dns::dns_server</code> as a single key.</p>

      <p>As always, you can override both defaults by specifying the
parameter yourself, like this:</p>

      <pre>
class {'profile::dns':
  dns_server =&gt; 'globaldns.puppetlabs.vm',
}
</pre>

      <p>The order of precedence is to check for class declaration parameters, then
hiera, then class defaults.</p>

    </div>

    <div class="instruction-header">
<i class="fa fa-desktop" />
Practice
</div>

    <div class="instruction-content">

      <p>Automatic Parameter Lookup is one of the ways in which Hiera can seem &#8220;magical&#8221;
to new users.  To help you get the hang of how it works, we&#8217;ve created a few
example classes that take parameters. Play around with it until it makes sense.</p>

      <p>For this exercise, you&#8217;ll be running <code>puppet agent -t</code> against a Puppet master.
For convenience, we&#8217;ve let you edit your environment on the master by mounting 
it to the <code>/root/puppetcode</code> directory.</p>

      <p>Look through the examples classes in <code>/root/puppetcode/modules/example/</code></p>

      <p>You can just declare the example classes in the <code>default</code> node definition in
<code>/root/puppetcode/manifests/site.pp</code> and run Puppet on your agent node.</p>

      <p>Hiera lookups are done on the master, so you&#8217;ll need to change the files in 
<code>/root/puppetcode/hieradata</code> which is mapped to your code environment.</p>

      <p>Because hiera.yaml exists on the master, you won&#8217;t be able to edit it directly.
The default configuration includes the <code>hieradata</code> directory in your
code environment.</p>

      <p>Try setting values in <code>common.yaml</code> and the yaml file that matches your node
in the <code>nodes</code> directory until you understand how automatic parameter lookup
works.</p>
    </div>

    <div class="instruction-header">
<i class="fa fa-pencil" />
</div>

    <div class="instruction-content">
      <p>Before you begin run <code>puppet agent -t</code> once to set up your node for this lesson.</p>

      <p>Remember, if you break something, just type <code>exit</code> and click <code>start session</code>
when it appears and you&#8217;ll get a new node and code environment.</p>
    </div>

  </div>

  <div id="terminal">
  <iframe id="try" src="https://try.puppet.com/sandbox/?course=get_hiera3" name="terminal" />
</div>

</div>