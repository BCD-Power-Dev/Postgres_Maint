-- search for duplicates in staging if needed
-- SELECT
-- lcn,
-- COUNT(*) AS cnt
-- FROM public.form_of_payment_staging
-- GROUP BY lcn
-- HAVING COUNT(*) > 1
-- ORDER BY cnt DESC;


WITH src AS (
    SELECT *
    FROM public.form_of_payment_staging
),
skipped AS (
    SELECT
        s.lcn,
        s.smid
    FROM src s
    WHERE NOT EXISTS (
        SELECT 1
        FROM public.form_of_payment t
        WHERE t.lcn = s.lcn
    )
),
upd AS (
    UPDATE public.form_of_payment t
    SET
        gcn               = s.gcn,
        account_id        = s.account_id,
        smid              = s.smid,
        smid_id           = s.smid_id,
        lcn_id            = s.lcn_id,
        air               = s.air,
        hotel             = s.hotel,
        car               = s.car,
        rail              = s.rail,
        other             = s.other,
        non_gds_carriers  = s.non_gds_carriers,
        non_profiled      = s.non_profiled,
        traveler_type     = s.traveler_type,
        notes             = s.notes,
        payment_summary   = s.payment_summary,
        traveler_type_id  = s.traveler_type_id,
        last_updated      = s.last_updated,
        updated_by        = s.updated_by,
        category          = s.category,
        summary           = s.summary
    FROM src s
    WHERE t.lcn = s.lcn
    RETURNING t.id, t.lcn
)
SELECT
    (SELECT COUNT(*) FROM src) AS staging_rows,
    (SELECT COUNT(*) FROM upd) AS updated_rows,
    (SELECT COUNT(*) FROM skipped) AS not_found_rows,
    (
        SELECT string_agg(lcn::text, ', ' ORDER BY lcn)
        FROM skipped
    ) AS not_found_lcns;
