.. _project-dir:

CASM Project Directory
^^^^^^^^^^^^^^^^^^^^^^

.. raw:: html

    <dl class="casm-list">
    <div style="display: flex; flex-direction: row; align-items: center; gap: 5px;">
        <dt class="casm-part">Location:</dt>
        <dd class="casm-part"><code>&lt;project&gt;/</code></dd>
    </div>
    <dt class="casm-part">Contents:</dt>
    <dd class="casm-part">
    <table class="casm-table">
        <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Format</th>
        </tr>
        <tr>
            <td><code>.casm/</code></td>
            <td>CASM project settings and data</td>
            <td><a class="only-light reference internal image-reference" href="#casm-dir"><img alt="link icon" class="only-light" src="../_static/link-16.svg"></a>
            <a class="only-dark reference internal image-reference" href="#casm-dir"><img alt="link icon" class="only-dark" src="../_static/link-16-dark.svg"></a>
        </tr>
        <tr>
            <td><code>basis_sets/</code></td>
            <td>Basis set input, results, and generated code</td>
            <td><a class="only-light reference internal image-reference" href="#basis-sets-dir"><img alt="link icon" class="only-light" src="../_static/link-16.svg"></a>
            <a class="only-dark reference internal image-reference" href="#basis-sets-dir"><img alt="link icon" class="only-dark" src="../_static/link-16-dark.svg"></a>
        </tr>
        <tr>
            <td><code>enumerations/</code></td>
            <td>Enumerated configurations, events, etc.</td>
            <td><a class="only-light reference internal image-reference" href="#enum-dir"><img alt="link icon" class="only-light" src="../_static/link-16.svg"></a>
            <a class="only-dark reference internal image-reference" href="#enum-dir"><img alt="link icon" class="only-dark" src="../_static/link-16-dark.svg"></a>
        </tr>
        <tr>
            <td><code>symmetry/</code></td>
            <td>Symmetry analysis results</td>
            <td><a class="only-light reference internal image-reference" href="#symmetry-dir"><img alt="link icon" class="only-light" src="../_static/link-16.svg"></a>
            <a class="only-dark reference internal image-reference" href="#symmetry-dir"><img alt="link icon" class="only-dark" src="../_static/link-16-dark.svg"></a>
        </tr>
    </table>
    </dd>
    </dl>

.. _casm-dir:

CASM project settings and data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. raw:: html

    <dl class="casm-list">
    <div style="display: flex; flex-direction: row; align-items: center; gap: 5px;">
        <dt class="casm-part">Location:</dt>
        <dd class="casm-part"><code>&lt;project&gt;/.casm/</code></dd>
    </div>
    <dt class="casm-part">Contents:</dt>
    <dd class="casm-part">
    <table class="casm-table">
        <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Format</th>
        </tr>
        <tr>
            <td><code>prim.json</code></td>
            <td>Primitive crystal structure and allowed degrees of freedom (DoF)</td>
            <td><a href="#prim">Prim</a></td>
        </tr>
        <tr>
            <td><code>chemical_composition_axes.json</code></td>
            <td>Chemical composition axes</td>
            <td><a href="TODO">Composition Axes</a></td>
        </tr>
        <tr>
            <td><code>occupant_composition_axes.jfson</code></td>
            <td>Occupant composition axes</td>
            <td><a href="TODO">Composition Axes</a></td>
        </tr>
        <tr>
            <td><code>project_settings.json</code></td>
            <td>CASM project settings</td>
            <td><a href="TODO">Project Settings</a></td>
        </tr>
    </table>
    </dd>
    <dt class="casm-part">Deprecated contents:</dt>
    <dd class="casm-part">
    <table class="casm-table">
        <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Format</th>
        </tr>
        <tr>
            <td><code>composition_axes.json</code></td>
            <td>Chemical composition axes, for v1 compatibility.</td>
            <td><a href="TODO">Composition Axes</a></td>
        </tr>
    </table>
    </dd>
    </dl>

