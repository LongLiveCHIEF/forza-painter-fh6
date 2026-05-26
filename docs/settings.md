# Configuration Settings

This document describes the user-configurable settings available in the forza-painter FH6 desktop app.

## Table of Contents

- [Quality Preset](#quality-preset)
- [Use Custom Settings](#use-custom-settings)
- [Max Preview Size](#max-preview-size)
- [Max Resolution](#max-resolution)
- [Max Threads](#max-threads)
- [Mutated Samples](#mutated-samples)
- [Enable Progressive Sampling](#enable-progressive-sampling)
- [Progressive Sampling Start](#progressive-sampling-start)
- [Progressive Sampling End](#progressive-sampling-end)
- [Progressive Sampling Transition](#progressive-sampling-transition)
- [Progressive Sampling Curve](#progressive-sampling-curve)
- [Error Grid Size](#error-grid-size)
- [Force Opaque Shapes](#force-opaque-shapes)
- [Posterize Levels](#posterize-levels)
- [Preview Every](#preview-every)
- [Random Samples](#random-samples)
- [Preprocess Mode](#preprocess-mode)
- [Save At](#save-at)
- [Save Every](#save-every)
- [Stop At](#stop-at)
- [Game Process](#game-process)
- [Template Layer Count](#template-layer-count)
- [Layer Count Address](#layer-count-address)
- [Layer Table Address](#layer-table-address)

## Quality Preset

The quality preset is the main starting point for generation settings. Each preset loads a bundled `.ini` profile with a tuned combination of resolution, sampling, preview frequency, and output layer targets.

Changing the preset affects nearly every part of generation quality and speed at once. Lower presets generate faster and are useful for composition checks. Higher presets produce cleaner geometry JSON with more detail, but they take longer and use more GPU time.

If you want to fine-tune individual values, pick the closest preset first and then enable custom settings.

## Use Custom Settings

This option lets you override the currently selected preset with your own values for the advanced generator settings.

When enabled, the app merges your custom values on top of the selected preset. This is useful when you like a preset overall but want to change one or two values such as `Random Samples`, `Stop At`, or `Max Resolution`.

Using custom settings can improve results for specific images, but it also makes it easier to create combinations that are much slower or less balanced than the bundled presets.

## Max Preview Size

`Max Preview Size` controls the preview image size requested from the generator while generation is running.

Higher values can produce larger intermediate preview PNG files, which may make progress previews easier to inspect. Lower values reduce preview image size and can slightly reduce preview write overhead.

This setting affects generator-produced preview files, not the final JSON shape count. The app still scales previews to fit the UI panel.

## Max Resolution

`Max Resolution` controls the working-size cap the generator uses for the source image.

Higher values let the generator preserve finer details, edges, and smaller features from the original image. Lower values make generation faster, but small details may be simplified or lost.

This setting does not directly change the size of the preview area in the app. Instead, it changes the level of detail available to the generator, which affects the quality of the generated JSON and any preview images produced from that generation.

## Max Threads

`Max Threads` controls how many threads the generator is allowed to use.

A value of `0` typically means the generator chooses automatically. Increasing this value may improve throughput on some systems, but it can also increase CPU contention or reduce responsiveness if the machine is doing other work.

This setting mainly affects performance rather than output appearance.

## Mutated Samples

`Mutated Samples` controls how many locally adjusted candidate shapes are tested during optimization.

Higher values usually help the generator refine shapes more carefully, which can improve local fit and reduce rough-looking geometry. Lower values are faster, but the result may be less polished.

Raising this value increases compute time. It usually works best when paired with enough random sampling and a suitable output layer target.

## Enable Progressive Sampling

`Enable Progressive Sampling` turns on progressive changes to sampling behavior over the course of generation.

When supported by the generator, this allows the app to begin with one sampling strategy and gradually transition to another as layer count increases.

This can help balance speed and quality across early and late generation stages, but it depends on generator support and on the related progressive sampling fields being configured sensibly.

## Progressive Sampling Start

`Progressive Sampling Start` defines the starting point for progressive sampling behavior.

Lower starting values can make early layers cheaper and faster. Higher starting values may put more work into early optimization.

This setting only matters when progressive sampling is enabled.

## Progressive Sampling End

`Progressive Sampling End` defines the ending point for progressive sampling behavior.

Higher end values can improve late-stage refinement, especially when chasing detail in larger outputs. Lower end values reduce that extra refinement effort.

This setting only matters when progressive sampling is enabled.

## Progressive Sampling Transition

`Progressive Sampling Transition` controls how quickly the generator moves from the progressive start behavior to the progressive end behavior.

A shorter transition shifts the generator into its late-stage behavior sooner. A longer transition keeps the early-stage behavior active for more of the run.

This setting only matters when progressive sampling is enabled.

## Progressive Sampling Curve

`Progressive Sampling Curve` controls the shape of the transition used by progressive sampling.

Depending on generator behavior, this can make the shift more gradual, more aggressive, or weighted toward the beginning or end of the run.

This setting only matters when progressive sampling is enabled, and it is mainly for advanced tuning.

## Error Grid Size

`Error Grid Size` controls the grid granularity used during generator error evaluation.

Changing it can affect how the generator estimates image error and chooses shapes. In practice, this can slightly change the quality/performance balance and the way detail is preserved.

Most users should leave this near preset defaults unless they are experimenting. Extreme values may produce unexpected quality or performance tradeoffs.

## Force Opaque Shapes

`Force Opaque Shapes` controls whether generated shapes are forced to be fully opaque.

When enabled, transparent or semi-transparent regions in the source image are more likely to become opaque filled shapes. When disabled, transparency can be preserved more naturally where the generator supports it.

For transparent PNG inputs, leaving this off usually gives more faithful results. Turning it on may be useful if you want stronger solid coverage, but it can make transparent areas look heavier.

## Posterize Levels

`Posterize Levels` controls how many color levels are available during generation.

Lower values simplify color variation, which can make results cleaner or more stylized but can also flatten gradients and subtle shading. Higher values preserve more color detail and tonal variation.

Increasing this value can improve fidelity, especially for images with soft gradients, but it may also increase complexity and runtime.

## Preview Every

`Preview Every` controls how often the generator writes updated preview images during generation.

Lower values write previews more frequently, which makes progress easier to watch but adds more disk activity and overhead. Higher values reduce preview update frequency and are usually more efficient.

This setting affects preview cadence, not final output quality directly.

## Random Samples

`Random Samples` controls how many random candidate shapes the generator tests.

This is one of the most important quality settings. Higher values usually produce clearer and more accurate results, especially on complex images or when trying to preserve fine detail. Lower values are much faster but can look rough or blurry.

Increasing `Random Samples` significantly increases generation time and GPU workload. The README already notes that values above `200000` often make a major quality difference.

## Preprocess Mode

`Preprocess Mode` controls whether the source image is transformed before generation.

The bundled presets currently use `none`, which means the original image is used as-is. Other modes, such as `luma_band`, can change contrast or tonal separation before generation.

Preprocessing can help certain images generate more cleanly, but it can also alter the source in ways that reduce fidelity if used incorrectly.

## Save At

`Save At` is a comma-separated list of layer counts where the generator should write checkpoint JSON files.

For example, a value like `500,1000,1500` tells the generator to save outputs at those milestone layer counts. This is useful when you want multiple candidate outputs from a single run.

More checkpoints give you more choices for import, but they also create more files. The final quality at each checkpoint depends on the rest of the generation settings.

## Save Every

`Save Every` controls how often the generator writes recurring checkpoint outputs.

Lower values create checkpoints more frequently. Higher values reduce checkpoint frequency and file clutter.

This setting is useful if you want a steady stream of intermediate JSON files without listing every target manually in `Save At`.

## Stop At

`Stop At` controls the target layer count where generation stops.

Higher values allow more shapes to be generated, which can increase detail and fidelity. Lower values stop earlier and produce simpler outputs more quickly.

This setting directly affects the expected JSON complexity and the minimum practical template size needed for import. Remember that FH6 import also reserves 4 extra boundary layers beyond the drawable shapes.

## Game Process

`Game Process` is the selected running FH6 process on the Import page.

This tells the app which game process to inspect and write into during import. In normal use, you should choose the running Forza Horizon 6 process from the refreshed list.

Choosing the wrong process will prevent import from working correctly.

## Template Layer Count

`Template Layer Count` is the exact in-game number of layers in the ungrouped FH6 template you prepared for import.

This value is required because the app uses it to validate capacity and to match the generated JSON against the available template layers.

If the value is wrong, import can fail or produce incomplete results. Also remember that FH6 needs 4 extra boundary layers, so a template must be slightly larger than the drawable layer count in the JSON.

## Layer Count Address

`Layer Count Address` is an advanced manual import field used to bypass the normal signature-based template lookup.

This is only meant for troubleshooting or for sessions where automatic location fails. When provided, the app uses the given live memory address for the template layer count.

Most users should leave this blank. Incorrect values can prevent import or point to invalid memory.

## Layer Table Address

`Layer Table Address` is an advanced manual import field for directly specifying the live FH6 layer-table pointer.

This is paired with manual import troubleshooting and should normally be left empty. If `Layer Count Address` is provided and `Layer Table Address` is left blank, the app can try to infer the table pointer from the count address in FH6 mode.

Most users should never need to set this manually. Wrong values can cause import failure.
